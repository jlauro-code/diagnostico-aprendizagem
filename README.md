import { useState } from "react";

const categorias = {
  "Dependência": [
    "Inicia ações apenas com comando",
    "Busca validação constante",
    "Paralisa diante do erro"
  ],
  "Autonomia": [
    "Toma iniciativa",
    "Decide com critérios próprios",
    "Usa bem tempo e recursos"
  ],
  "Metacognição": [
    "Reconhece o que sabe e não sabe",
    "Monitora o próprio processo",
    "Avalia resultados"
  ],
  "Intervenção": [
    "Aplica o que aprende",
    "Propõe soluções",
    "Gera impacto na realidade"
  ]
};

const pesos = {
  "Dependência": 0.2,
  "Autonomia": 0.25,
  "Metacognição": 0.3,
  "Intervenção": 0.25
};

export default function App() {
  const [respostas, setRespostas] = useState({});
  const [resultado, setResultado] = useState(null);

  const setResposta = (cat, idx, val) => {
    setRespostas(prev => ({
      ...prev,
      { ...(prev[cat] || {}), Number(val) }
    }));
  };

  const calcular = () => {
    const indices = {};
    let global = 0;

    Object.keys(categorias).forEach(cat => {
      const vals = Object.values(respostas[cat] || {});
      if (vals.length === 0) return;
      const media = vals.reduce((a, b) => a + b, 0) / vals.length;
      indices[cat] = media;
      global += media * pesos[cat];
    });

    setResultado({ indices, global });
  };

  return (
    <div style={{ maxWidth: 700, margin: "0 auto", fontFamily: "Arial" }}>
      <h1>Diagnóstico do Processo de Aprendizagem</h1>

      {Object.entries(categorias).map(([cat, itens]) => (
        <div
          key={cat}
          style={{ border: "1px solid #ccc", padding: 16, marginBottom: 16 }}
        >
          <h2>{cat}</h2>
          {itens.map((q, i) => (
            <div
              key={i}
              style={{
                display: "flex",
                justifyContent: "space-between",
                marginBottom: 8
              }}
            >
              <span>{q}</span>
              <select onChange={e => setResposta(cat, i, e.target.value)}>
                <option value="">–</option>
                {[1, 2, 3, 4, 5].map(n => (
                  <option key={n} value={n}>{n}</option>
                ))}
              </select>
            </div>
          ))}
        </div>
      ))}

      <button
        onClick={calcular}
        style={{ padding: 10, fontSize: 16 }}
      >
        Gerar meu relatório
      </button>

      {resultado && (
        <div
          style={{
            marginTop: 24,
            padding: 16,
            border: "2px solid #333"
          }}
        >
          <h2>Seu resultado</h2>
          {Object.entries(resultado.indices).map(([cat, val]) => (
            <p key={cat}>
              <strong>{cat}:</strong> {val.toFixed(2)}
            </p>
          ))}
          <p>
            <strong>Índice geral:</strong> {resultado.global.toFixed(2)}
          </p>
          <p style={{ marginTop: 12 }}>
            Sugestão: observe as categorias com menor pontuação.
            Para avançar, procure refletir conscientemente sobre como aprende
            (metacognição) e aplicar seus conhecimentos em situações reais
            (intervenção).
          </p>
        </div>
      )}
    </div>
  );
}
