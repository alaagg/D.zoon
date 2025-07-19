import tkinter as tk from tkinter import ttk import threading

from adaptive_genetic_nonce_finder import adaptive_genetic

class NonceMinerGUI: def init(self, root): self.root = root self.root.title("🔥 Smart Nonce Genetic Miner") self.root.geometry("650x500")

self.status = tk.StringVar()
    self.status.set("اضغط 'ابدأ التعدين' للبحث عن Golden Nonce")

    self.setup_widgets()

def setup_widgets(self):
    padding = {'padx': 10, 'pady': 5}

    # إدخال بيانات رأس البلوك
    self.block_header_lbl = ttk.Label(self.root, text="رأس البلوك (Block Header - hex):")
    self.block_header_lbl.pack(**padding)
    self.block_header_entry = tk.Text(self.root, height=3, width=80)
    self.block_header_entry.insert(tk.END, "00000020f61e805a04069b0b1fce7706b5d13428703b5f54"
                                          "c0fda5bb0b00000000000000c1b725299be481c9e4f38632"
                                          "75cc2281e8810a9a1b6f8be3e36c1762b52db888")
    self.block_header_entry.pack(**padding)

    self.target_lbl = ttk.Label(self.root, text="الهدف Target (hex):")
    self.target_lbl.pack(**padding)
    self.target_entry = ttk.Entry(self.root, width=80)
    self.target_entry.insert(0, "000000000000000000000017ce11f285631c553a0c1123a23260dd5da7328084")
    self.target_entry.pack(**padding)

    self.start_btn = ttk.Button(self.root, text="ابدأ التعدين الذكي", command=self.start_mining)
    self.start_btn.pack(**padding)

    self.progress = ttk.Progressbar(self.root, mode='indeterminate')
    self.progress.pack(fill='x', **padding)

    self.status_lbl = ttk.Label(self.root, textvariable=self.status, wraplength=620, justify="center")
    self.status_lbl.pack(**padding)

    self.result_lbl = tk.Text(self.root, height=10, width=80)
    self.result_lbl.pack(**padding)

def start_mining(self):
    self.status.set("⏳ جاري التعدين باستخدام خوارزمية جينية متكيفة...")
    self.result_lbl.delete("1.0", tk.END)
    self.progress.start()
    threading.Thread(target=self.run_miner, daemon=True).start()

def run_miner(self):
    block_header_hex = self.block_header_entry.get("1.0", tk.END).strip().replace("\n", "")
    target_hex = self.target_entry.get().strip()

    # مبدئيًا، لا نستخدم هذه القيم في الخوارزمية، لكن يمكن تمريرها مستقبلًا للضغط

    best_nonce, fitness_score = adaptive_genetic(
        pop_size=300,
        elite_size=30,
        generations=150,
        init_mut=0.1,
        min_mut=0.001,
        max_mut=0.2,
        tour_size=5,
        improve_thresh=500
    )

    self.progress.stop()
    result = f"\n🎯 أفضل نونس تم العثور عليه:\nNonce: {best_nonce}\nFitness: {fitness_score}\n"
    self.status.set("✅ تم! النونس الذهبي جاهز")
    self.result_lbl.insert(tk.END, result)

if name == "main": root = tk.Tk() app = NonceMinerGUI(root) root.mainloop()

