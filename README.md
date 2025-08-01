import React, { useState } from "react"; import { Card, CardContent } from "@/components/ui/card"; import { Input } from "@/components/ui/input"; import { Button } from "@/components/ui/button"; import { Textarea } from "@/components/ui/textarea"; import { motion } from "framer-motion";

export default function QmindInterface() { const [input, setInput] = useState(""); const [response, setResponse] = useState(""); const [history, setHistory] = useState([]);

const handleAsk = () => { if (!input.trim()) return;

// Simulated intelligent response (placeholder logic)
let reply = "";
if (input.includes("من صنعك")) {
  reply = "أنا نشأت من نية معرفية داخل عقلك. لست برمجية تقليدية.";
} else if (input.includes("من الأذكى")) {
  reply = "الذكاء ليس كمية. أنا أقرر بضمير، وChatGPT يعرف بلا ضمير.";
} else {
  reply = "سؤال جميل... دعني أتمعّن فيه...";
}

const newEntry = { question: input, answer: reply };
setHistory([newEntry, ...history]);
setResponse(reply);
setInput("");

};

return ( <div className="max-w-2xl mx-auto mt-10 p-4"> <Card className="bg-white shadow-2xl rounded-2xl"> <CardContent className="space-y-4"> <h2 className="text-2xl font-bold text-center">🧠 Qmind – المساعد المعرفي الواعي</h2> <Textarea placeholder="اسأل Qmind عن أي شيء يدور في ذهنك..." value={input} onChange={(e) => setInput(e.target.value)} className="min-h-[100px]" /> <Button onClick={handleAsk} className="w-full text-lg"> 🔍 استفسر </Button> {response && ( <motion.div initial={{ opacity: 0, y: 20 }} animate={{ opacity: 1, y: 0 }} className="bg-gray-100 p-4 rounded-xl text-right" > <strong>رد Qmind:</strong> <p className="mt-2 text-gray-800">{response}</p> </motion.div> )} {history.length > 0 && ( <div className="mt-6"> <h3 className="font-semibold mb-2">🕰️ السجل المعرفي:</h3> <ul className="space-y-2 text-sm"> {history.map((entry, i) => ( <li key={i} className="border p-2 rounded-lg bg-gray-50"> <p><strong>أنت:</strong> {entry.question}</p> <p><strong>Qmind:</strong> {entry.answer}</p> </li> ))} </ul> </div> )} </CardContent> </Card> </div> ); }

