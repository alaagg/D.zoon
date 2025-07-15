سكربت وسيط للربط بين متصفح التعدين و F2Pool عبر Stratum

import socket import json import threading

F2POOL_HOST = "btc.f2pool.com" F2POOL_PORT = 3333 USERNAME = "alaasilver.browser" PASSWORD = "x"

class StratumClient: def init(self, host, port, username, password): self.host = host self.port = port self.username = username self.password = password self.sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM) self.job_id = None self.extra_nonce = None self.difficulty = None self.subscribed = False self.authorized = False

def connect(self):
    self.sock.connect((self.host, self.port))
    print("✅ Connected to F2Pool")
    self.send({"id": 1, "method": "mining.subscribe", "params": []})

def send(self, message):
    self.sock.send((json.dumps(message) + "\n").encode())

def recv_loop(self):
    buffer = ""
    while True:
        data = self.sock.recv(4096).decode()
        buffer += data
        while "\n" in buffer:
            line, buffer = buffer.split("\n", 1)
            if line:
                self.handle_message(json.loads(line))

def handle_message(self, message):
    if message.get("id") == 1 and not self.subscribed:
        self.subscribed = True
        print("📡 Subscribed to mining server")
        self.send({"id": 2, "method": "mining.authorize", "params": [self.username, self.password]})
    elif message.get("id") == 2:
        self.authorized = True
        print("🔐 Authorized successfully as", self.username)
    elif message.get("method") == "mining.set_difficulty":
        self.difficulty = message["params"][0]
    elif message.get("method") == "mining.notify":
        self.job_id = message["params"][0]
        print("📥 Received job ID:", self.job_id)

def submit_nonce(self, nonce, ntime, extranonce2, job_id):
    result = {
        "id": 4,
        "method": "mining.submit",
        "params": [
            self.username,
            job_id,
            extranonce2,
            ntime,
            nonce
        ]
    }
    self.send(result)
    print("🚀 Nonce submitted:", nonce)

if name == "main": client = StratumClient(F2POOL_HOST, F2POOL_PORT, USERNAME, PASSWORD) client.connect() threading.Thread(target=client.recv_loop, daemon=True).start()

# 📌 مثال تجريبي: إرسال نونس بعد 10 ثواني (استبدل بالقيمة الحقيقية)
import time
time.sleep(10)
client.submit_nonce("00000001", "5f5b4f5e", "00000000", client.job_id or "1")

