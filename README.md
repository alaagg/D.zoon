#!/usr/bin/env python3
from flask import Flask, request, jsonify, render_template_string
import hashlib
import random
import struct
import threading
import requests
from bs4 import BeautifulSoup

# ============ الخوارزمية الجينية ============
class GeneticNonceMiner:
    def __init__(self):
        self.current_job = None
        self.results = []
        self.K = [
            0x428a2f98, 0x71374491, 0xb5c0fbcf, 0xe9b5dba5, 0x3956c25b,
            0x59f111f1, 0x923f82a4, 0xab1c5ed5, 0xd807aa98, 0x12835b01,
            0x243185be, 0x550c7dc3, 0x72be5d74, 0x80deb1fe, 0x9bdc06a7,
            0xc19bf174, 0xe49b69c1, 0xefbe4786, 0x0fc19dc6, 0x240ca1cc,
            0x2de92c6f, 0x4a7484aa, 0x5cb0a9dc, 0x76f988da, 0x983e5152,
            0xa831c66d, 0xb00327c8, 0xbf597fc7, 0xc6e00bf3, 0xd5a79147,
            0x06ca6351, 0x14292967, 0x27b70a85, 0x2e1b2138, 0x4d2c6dfc,
            0x53380d13, 0x650a7354, 0x766a0abb, 0x81c2c92e, 0x92722c85,
            0xa2bfe8a1, 0xa81a664b, 0xc24b8b70, 0xc76c51a3, 0xd192e819,
            0xd6990624, 0xf40e3585, 0x106aa070, 0x19a4c116, 0x1e376c08,
            0x2748774c, 0x34b0bcb5, 0x391c0cb3, 0x4ed8aa4a, 0x5b9cca4f,
            0x682e6ff3, 0x748f82ee, 0x78a5636f, 0x84c87814, 0x8cc70208,
            0x90befffa, 0xa4506ceb, 0xbef9a3f7, 0xc67178f2
        ]
        self.IV = [
            0x6a09e667, 0xbb67ae85, 0x3c6ef372, 0xa54ff53a,
            0x510e527f, 0x9b05688c, 0x1f83d9ab, 0x5be0cd19
        ]

    def rotr(self, x, n):
        return ((x >> n) | (x << (32 - n))) & 0xffffffff

    def sha256_compress(self, block, state):
        W = list(struct.unpack(">16L", block))
        for t in range(16, 64):
            s0 = self.rotr(W[t-15], 7) ^ self.rotr(W[t-15], 18) ^ (W[t-15] >> 3)
            s1 = self.rotr(W[t-2], 17) ^ self.rotr(W[t-2], 19) ^ (W[t-2] >> 10)
            W.append((W[t-16] + s0 + W[t-7] + s1) & 0xffffffff)
        
        a, b, c, d, e, f, g, h = state
        for t in range(64):
            S1 = self.rotr(e, 6) ^ self.rotr(e, 11) ^ self.rotr(e, 25)
            ch = (e & f) ^ (~e & g)
            temp1 = (h + S1 + ch + self.K[t] + W[t]) & 0xffffffff
            S0 = self.rotr(a, 2) ^ self.rotr(a, 13) ^ self.rotr(a, 22)
            maj = (a & b) ^ (a & c) ^ (b & c)
            temp2 = (S0 + maj) & 0xffffffff
            
            h, g, f, e, d, c, b, a = g, f, e, (d + temp1) & 0xffffffff, c, b, a, (temp1 + temp2) & 0xffffffff
        
        return [(state[i] + [a, b, c, d, e, f, g, h][i]) & 0xffffffff for i in range(8)]

    def calculate_hash(self, block_header, nonce):
        data = f"{block_header}{nonce}".encode()
        return hashlib.sha256(hashlib.sha256(data).digest()).hexdigest()

    def fitness(self, hash_hex, target_hex):
        hash_int = int(hash_hex, 16)
        target_int = int(target_hex, 16)
        if hash_int <= target_int:
            return float('inf')
        return 1.0 / (hash_int - target_int + 1)

    def crossover(self, p1, p2):
        point = random.randint(1, 31)
        mask = (1 << point) - 1
        return (p1 & mask) | (p2 & ~mask)

    def mutate(self, individual, rate):
        if random.random() < rate:
            bit = 1 << random.randrange(32)
            return individual ^ bit
        return individual

    def mine(self, block_header, target, pop_size=200, generations=100):
        population = [random.getrandbits(32) for _ in range(pop_size)]
        best_nonce, best_fit = None, -float('inf')
        
        for gen in range(generations):
            scored = []
            for nonce in population:
                current_hash = self.calculate_hash(block_header, nonce)
                current_fit = self.fitness(current_hash, target)
                scored.append((nonce, current_fit))
                if current_fit > best_fit:
                    best_nonce, best_fit = nonce, current_fit
                    if current_fit == float('inf'):
                        return best_nonce, current_hash
            
            scored.sort(key=lambda x: x[1], reverse=True)
            elites = [n for n, _ in scored[:20]]
            
            new_pop = elites.copy()
            while len(new_pop) < pop_size:
                p1, p2 = random.choices(elites, k=2)
                child = self.crossover(p1, p2)
                child = self.mutate(child, 0.1 * (1 - gen/generations))
                new_pop.append(child)
            
            population = new_pop
        
        return best_nonce, self.calculate_hash(block_header, best_nonce)

# ============ واجهة الويب والاتصال بالشبكة ============
app = Flask(__name__)
miner = GeneticNonceMiner()

HTML_TEMPLATE = """
<!DOCTYPE html>
<html>
<head>
    <title>منصة التعدين الجيني</title>
    <style>
        body { font-family: Arial, sans-serif; max-width: 800px; margin: auto; padding: 20px; }
        .form-group { margin-bottom: 15px; }
        textarea, input { width: 100%; padding: 8px; }
        button { background: #4CAF50; color: white; border: none; padding: 10px 15px; cursor: pointer; }
        .results { margin-top: 20px; border-top: 1px solid #eee; padding-top: 15px; }
    </style>
</head>
<body>
    <h1>منصة التعدين الجيني للـ Nonce</h1>
    
    <form id="miningForm">
        <div class="form-group">
            <label>رأس البلوك (Block Header):</label>
            <textarea name="block_header" rows="3" required>{{ default_block }}</textarea>
        </div>
        
        <div class="form-group">
            <label>الهدف (Target):</label>
            <input type="text" name="target" value="{{ default_target }}" required>
        </div>
        
        <button type="button" onclick="startMining()">بدء التعدين</button>
        <button type="button" onclick="fetchLatest()">جلب أحدث بلوك</button>
    </form>
    
    <div id="status" style="margin: 15px 0; font-weight: bold;"></div>
    
    <div class="results">
        <h3>النتائج:</h3>
        <div id="results"></div>
    </div>
    
    <script>
        function startMining() {
            const formData = new FormData(document.getElementById('miningForm'));
            document.getElementById('status').textContent = 'جاري التعدين...';
            
            fetch('/start_mining', {
                method: 'POST',
                body: new URLSearchParams(formData)
            })
            .then(response => response.json())
            .then(data => {
                document.getElementById('status').textContent = 'تم الانتهاء!';
                document.getElementById('results').innerHTML += `
                    <div style="margin: 10px 0; padding: 10px; background: #f5f5f5;">
                        <p><strong>Nonce:</strong> ${data.nonce}</p>
                        <p><strong>Hash:</strong> ${data.hash}</p>
                        <p><strong>Valid:</strong> ${data.valid ? '✅ صالح' : '❌ غير صالح'}</p>
                    </div>
                `;
            });
        }
        
        function fetchLatest() {
            document.getElementById('status').textContent = 'جاري جلب أحدث بلوك...';
            fetch('/fetch_latest_block')
            .then(response => response.json())
            .then(data => {
                document.querySelector('[name="block_header"]').value = data.block_header;
                document.querySelector('[name="target"]').value = data.target;
                document.getElementById('status').textContent = 'تم جلب أحدث بلوك!';
            });
        }
    </script>
</body>
</html>
"""

@app.route('/')
def index():
    default_block = "00000020f61e805a04069b0b1fce7706b5d13428703b5f54c0fda5bb0b00000000000000c1b725299be481c9e4f3863275cc2281e8810a9a1b6f8be3e36c1762b52db888"
    default_target = "000000000000000000000017ce11f285631c553a0c1123a23260dd5da7328084"
    return render_template_string(HTML_TEMPLATE, default_block=default_block, default_target=default_target)

@app.route('/fetch_latest_block')
def fetch_latest_block():
    try:
        url = "https://blockchain.info/latestblock"
        data = requests.get(url).json()
        block_header = requests.get(f"https://blockchain.info/rawblock/{data['hash']}").json()['hash']
        target = data['bits']
        return jsonify({"block_header": block_header, "target": target})
    except Exception as e:
        return jsonify({"error": str(e)}), 500

@app.route('/start_mining', methods=['POST'])
def start_mining():
    block_header = request.form['block_header']
    target = request.form['target']
    
    nonce, hash_result = miner.mine(block_header, target)
    
    return jsonify({
        "nonce": nonce,
        "hash": hash_result,
        "valid": int(hash_result, 16) <= int(target, 16)
    })

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000, debug=True)
