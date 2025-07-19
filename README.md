import struct
import random
import hashlib
from decimal import Decimal

# ===== SHA-256 Constants =====
K = [
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

def rotr(x, n):
    return ((x >> n) | (x << (32 - n))) & 0xffffffff

def sigma0(x):
    return rotr(x, 7) ^ rotr(x, 18) ^ (x >> 3)

def sigma1(x):
    return rotr(x, 17) ^ rotr(x, 19) ^ (x >> 10)

def sha256_compress(block, state):
    W = list(struct.unpack(">16L", block))
    for t in range(16, 64):
        W.append((W[t-16] + sigma0(W[t-15]) + W[t-7] + sigma1(W[t-2])) & 0xffffffff)
    a, b, c, d, e, f, g, h = state
    for t in range(64):
        S1 = rotr(e, 6) ^ rotr(e, 11) ^ rotr(e, 25)
        ch = (e & f) ^ (~e & g)
        temp1 = (h + S1 + ch + K[t] + W[t]) & 0xffffffff
        S0 = rotr(a, 2) ^ rotr(a, 13) ^ rotr(a, 22)
        maj = (a & b) ^ (a & c) ^ (b & c)
        temp2 = (S0 + maj) & 0xffffffff
        h, g, f, e, d, c, b, a = g, f, e, (d + temp1) & 0xffffffff, c, b, a, (temp1 + temp2) & 0xffffffff
    return [(x + y) & 0xffffffff for x, y in zip(state, [a, b, c, d, e, f, g, h])]

# ===== Midstate for Block 700000 =====
IV = [
    0x6a09e667, 0xbb67ae85,
    0x3c6ef372, 0xa54ff53a,
    0x510e527f, 0x9b05688c,
    0x1f83d9ab, 0x5be0cd19
]

def hex_le(hexstr):
    return bytes.fromhex(hexstr)[::-1]

# Block 700000 header part 1 (64 bytes)
block1 = (
    bytes.fromhex("00000020") +  # version
    hex_le("0000000000000000000c9b492cd660f2fd2fb5048a33d3d1d6e2758e24bfe9ba") +
    hex_le("0e3e2357e806b6cdb1f70f5c8c6b0fa124cb8f604da0f852f5bd91c8d1c00f02")
)[:64]

midstate = sha256_compress(block1, IV)

# ===== Fitness Function =====
def fitness(nonce):
    payload = (
        struct.pack("<I", 0x613CB1E2) +  # timestamp
        struct.pack("<I", 0x170cebef) +  # bits
        struct.pack("<I", nonce)        # nonce
    )
    block2 = (
        payload +
        b'\x80' +
        b'\x00' * (64 - len(payload) - 1 - 8) +
        struct.pack(">Q", 80 * 8)
    )
    H2 = sha256_compress(block2, midstate)
    h_final = hashlib.sha256(b''.join(struct.pack(">L", h) for h in H2)).digest()[::-1]
    return -int.from_bytes(h_final, "big")

# ===== Genetic Algorithm with Heatmap Mutation =====
def genetic_search(pop_size, generations, mut_rate):
    heat = [1.0] * 32
    decay = 0.9
    eps = 1e-3
    pop = [random.getrandbits(32) for _ in range(pop_size)]
    best_nonce, best_fit = None, None

    for gen in range(generations):
        scored = [(n, fitness(n)) for n in pop]
        scored.sort(key=lambda x: x[1], reverse=True)
        curr_best, curr_fit = scored[0]

        if best_fit is None or curr_fit > best_fit:
            best_fit, best_nonce = curr_fit, curr_best

        # Heat-based probabilities
        total = sum(max(h, 0) + eps for h in heat)
        probs = [(max(h, 0) + eps) / total for h in heat]

        # Elitism
        elites = [n for n, _ in scored[:pop_size // 10]]
        new_pop = elites.copy()

        # Crossover + mutation
        while len(new_pop) < pop_size:
            p1, p2 = random.sample(elites, 2)
            pt = random.randint(1, 31)
            mask = (1 << pt) - 1
            child = (p1 & mask) | (p2 & ~mask)

            if random.random() < mut_rate:
                bit = random.choices(range(32), weights=probs)[0]
                before = fitness(child)
                child ^= 1 << bit
                after = fitness(child)
                if after > before:
                    heat[bit] += (after - before)

            new_pop.append(child & 0xffffffff)

        heat = [h * decay for h in heat]
        pop = new_pop

    return best_nonce, best_fit

# ===== Run the Search =====
if __name__ == "__main__":
    TARGET_NONCE = 2539189940
    POP = 200
    GEN = 300
    MUT = 0.05

    result_nonce, result_fit = genetic_search(POP, GEN, MUT)
    error = abs(Decimal(result_nonce) - Decimal(TARGET_NONCE)) / Decimal(TARGET_NONCE) * 100

    print(f"🔍 Best nonce found: {result_nonce}")
    print(f"📉 Error: {error:.3f}%")
