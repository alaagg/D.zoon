// Albasatneh Zeta Zero Visualizer import React, { useState } from "react"; import Plot from "react-plotly.js"; import { Input } from "@/components/ui/input"; import { Button } from "@/components/ui/button";

export default function ZetaZeroPage() { const [k, setK] = useState(1); const [zeros, setZeros] = useState([]);

// الثوابت الرسمية const C_0 = -6.180555; const f = 1.0; const beta = [ 0.774963, -0.225223, 0.053304, -0.010113, 0.001562, -0.0002, 0.00002, -0.000002, 0.0000001, 0.0 ];

const ln = Math.log;

function R_k_eff(k) { const x = ln(k) / ln(ln(k)); return beta.reduce((sum, b, n) => sum + b * Math.pow(x, n), 0); }

function t_k(k) { return (2 * Math.PI * k + C_0 + R_k_eff(k)) / f; }

function computeZero() { const kVal = parseInt(k); if (!isNaN(kVal) && kVal > 0) { const t = t_k(kVal); setZeros([...zeros, { real: 0.5, imag: t, k: kVal }]); } }

return ( <div className="p-6 max-w-3xl mx-auto"> <h1 className="text-2xl font-bold mb-4">🎯 Albasatneh Zeta Zero Visualizer</h1> <div className="flex gap-2 mb-4"> <Input type="number" value={k} onChange={(e) => setK(e.target.value)} placeholder="أدخل رقم الجذر k" /> <Button onClick={computeZero}>احسب الصفر وأضف للرسم</Button> </div>

<Plot
    data={[
      {
        x: zeros.map((z) => z.real),
        y: zeros.map((z) => z.imag),
        type: "scatter",
        mode: "markers+text",
        marker: { size: 8 },
        text: zeros.map((z) => `k=${z.k}`),
        name: "جذور زيتا"
      },
      {
        x: [0.5, 0.5],
        y: [0, Math.max(...zeros.map((z) => z.imag), 20)],
        type: "scatter",
        mode: "lines",
        line: { dash: "dash", color: "red" },
        name: "الخط الحرج Re = 0.5"
      }
    ]}
    layout={{
      title: "رسم الأصفار غير البديهية",
      xaxis: { title: "Re(s)", range: [0, 1] },
      yaxis: { title: "Im(s)", autorange: true },
      height: 600
    }}
  />
</div>

); }

