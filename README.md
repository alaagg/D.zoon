import React, { useState } from "react"; import { Input } from "@/components/ui/input"; import { Button } from "@/components/ui/button";

// === SHA-256 === function rotr(n, x) { return (x >>> n) | (x << (32 - n)); }

function sha256_compress(block, state) { const K = [ 0x428a2f98, 0x71374491, 0xb5c0fbcf, 0xe9b5dba5, 0x3956c25b, 0x59f111f1, 0x923f82a4, 0xab1c5ed5, 0xd807aa98, 0x12835b01, 0x243185be, 0x550c7dc3, 0x72be5d74, 0x80deb1fe, 0x9bdc06a7, 0xc19bf174, 0xe49b69c1, 0xefbe4786, 0x0fc19dc6, 0x240ca1cc, 0x2de92c6f, 0x4a7484aa, 0x5cb0a9dc, 0x76f988da, 0x983e5152, 0xa831c66d, 0xb00327c8, 0xbf597fc7, 0xc6e00bf3, 0xd5a79147, 0x06ca6351, 0x14292967, 0x27b70a85, 0x2e1b2138, 0x4d2c6dfc, 0x53380d13, 0x650a7354, 0x766a0abb, 0x81c2c92e, 0x92722c85, 0xa2bfe8a1, 0xa81a664b, 0xc24b8b70, 0xc76c51a3, 0xd192e819, 0xd6990624, 0xf40e3585, 0x106aa070, 0x19a4c116, 0x1e376c08, 0x2748774c, 0x34b0bcb5, 0x391c0cb3, 0x4ed8aa4a, 0x5b9cca4f, 0x682e6ff3, 0x748f82ee, 0x78a5636f, 0x84c87814, 0x8cc70208, 0x90befffa, 0xa4506ceb, 0xbef9a3f7, 0xc67178f2 ];

let W = new Array(64); for (let i = 0; i < 16; i++) { W[i] = (block[i * 4] << 24) | (block[i * 4 + 1] << 16) | (block[i * 4 + 2] << 8) | block[i * 4 + 3]; } for (let i = 16; i < 64; i++) { const s0 = rotr(7, W[i - 15]) ^ rotr(18, W[i - 15]) ^ (W[i - 15] >>> 3); const s1 = rotr(17, W[i - 2]) ^ rotr(19, W[i - 2]) ^ (W[i - 2] >>> 10); W[i] = (W[i - 16] + s0 + W[i - 7] + s1) >>> 0; }

let [a, b, c, d, e, f, g, h] = state;

for (let i = 0; i < 64; i++) { const S1 = rotr(6, e) ^ rotr(11, e) ^ rotr(25, e); const ch = (e & f) ^ (~e & g); const temp1 = (h + S1 + ch + K[i] + W[i]) >>> 0; const S0 = rotr(2, a) ^ rotr(13, a) ^ rotr(22, a); const maj = (a & b) ^ (a & c) ^ (b & c); const temp2 = (S0 + maj) >>> 0;

h = g;
g = f;
f = e;
e = (d + temp1) >>> 0;
d = c;
c = b;
b = a;
a = (temp1 + temp2) >>> 0;

}

return [ (state[0] + a) >>> 0, (state[1] + b) >>> 0, (state[2] + c) >>> 0, (state[3] + d) >>> 0, (state[4] + e) >>> 0, (state[5] + f) >>> 0, (state[6] + g) >>> 0, (state[7] + h) >>> 0, ]; }

function doubleSHA256(input) { const IV = [ 0x6a09e667, 0xbb67ae85, 0x3c6ef372, 0xa54ff53a, 0x510e527f, 0x9b05688c, 0x1f83d9ab, 0x5be0cd19 ]; const msg = new Uint8Array(64); input.forEach((val, i) => msg.set(val, i)); msg[input.length] = 0x80; const bitLen = input.length * 8; msg[63] = bitLen;

const H1 = sha256_compress(msg, IV);

const block2 = new Uint8Array(64); for (let i = 0; i < 32; i++) block2[i] = (H1[i >> 2] >>> (24 - 8 * (i % 4))) & 0xff; block2[32] = 0x80; block2[63] = 256;

const H2 = sha256_compress(block2, IV);

return H2.map(h => h.toString(16).padStart(8, '0')).join(''); }

export default function MinerInterface() { const [fields, setFields] = useState({ version: "", prev: "", merkle: "", time: "", bits: "", nonce: "" }); const [hash, setHash] = useState("");

const handleChange = (e) => { setFields({ ...fields, [e.target.name]: e.target.value }); };

const computeHash = () => { try { const hexString = fields.version.padStart(8, '0') + fields.prev.padStart(64, '0') + fields.merkle.padStart(64, '0') + fields.time.padStart(8, '0') + fields.bits.padStart(8, '0') + fields.nonce.padStart(8, '0');

const input = new Uint8Array(hexString.match(/.{1,2}/g).map(b => parseInt(b, 16)));
  const h = doubleSHA256(input);
  setHash(h);
} catch (e) {
  setHash("خطأ في الإدخال أو التنسيق.");
}

};

return ( <div className="bg-black text-white min-h-screen p-6 space-y-4"> <h1 className="text-xl font-bold">لوحة تعدين SHA-256</h1> <div className="grid grid-cols-2 md:grid-cols-3 gap-4"> {Object.entries(fields).map(([key, val]) => ( <Input
key={key}
name={key}
placeholder={key}
value={val}
onChange={handleChange}
className="bg-zinc-900 text-white border border-zinc-700"
/> ))} </div> <Button onClick={computeHash} className="bg-green-600 hover:bg-green-700 text-white">تشغيل الخوارزمية</Button> <div className="break-words p-4 bg-zinc-900 border border-zinc-700 rounded"> <strong>الهاش الناتج:</strong> <p>{hash}</p> </div> </div> ); }

