import { useState, useEffect } from "react";
import { Card, CardContent } from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import { Input } from "@/components/ui/input";


export default function FinanceApp() {
const [entradas, setEntradas] = useState([]);
const [gastos, setGastos] = useState([]);
const [valor, setValor] = useState("");
const [desc, setDesc] = useState("");
const [categoria, setCategoria] = useState("Alimentação");
const [meta, setMeta] = useState(0);


// carregar dados
useEffect(() => {
const data = JSON.parse(localStorage.getItem("finance"));
if (data) {
setEntradas(data.entradas || []);
setGastos(data.gastos || []);
setMeta(data.meta || 0);
}
}, []);


// salvar dados
useEffect(() => {
localStorage.setItem(
"finance",
JSON.stringify({ entradas, gastos, meta })
);
}, [entradas, gastos, meta]);


const addEntrada = () => {
if (!valor) return;
setEntradas([
...entradas,
{ valor: parseFloat(valor), desc, data: new Date() },
]);
setValor("");
setDesc("");
};


const addGasto = () => {
if (!valor) return;
setGastos([
...gastos,
{ valor: parseFloat(valor), desc, categoria, data: new Date() },
]);
setValor("");
setDesc("");
};


const totalEntradas = entradas.reduce((a, b) => a + b.valor, 0);
const totalGastos = gastos.reduce((a, b) => a + b.valor, 0);
const saldo = totalEntradas - totalGastos;


const categorias = [
"Alimentação",
"Transporte",
"Moradia",
"Lazer",
"Saúde",
"Educação",
"Outros",
];


const gastoPorCategoria = categorias.map((cat) => {
const total = gastos
.filter((g) => g.categoria === cat)
.reduce((a, b) => a + b.valor, 0);
return { cat, total };
});


return (



💰 Sistema Financeiro PRO



  {/* RESUMO */}
  <Card>
    <CardContent className="p-4 grid gap-2">
      <h2 className="font-bold">Resumo</h2>
      <p>Entradas: R$ {totalEntradas.toFixed(2)}</p>
      <p>Gastos: R$ {totalGastos.toFixed(2)}</p>
      <p>Saldo: R$ {saldo.toFixed(2)}</p>

      {totalGastos > totalEntradas && (
        <p className="text-red-500">⚠️ Gastando demais!</p>
      )}

      {saldo >= meta && meta > 0 && (
        <p className="text-green-500">🎯 Meta atingida!</p>
      )}
    </CardContent>
  </Card>

  {/* META */}
  <Card>
    <CardContent className="p-4 grid gap-2">
      <h2 className="font-bold">Meta de Economia</h2>
      <Input
        type="number"
        placeholder="Defina sua meta"
        value={meta}
        onChange={(e) => setMeta(parseFloat(e.target.value))}
      />
    </CardContent>
  </Card>

  {/* ADICIONAR */}
  <Card>
    <CardContent className="p-4 grid gap-2">
      <h2 className="font-bold">Adicionar</h2>
      <Input
        placeholder="Descrição"
        value={desc}
        onChange={(e) => setDesc(e.target.value)}
      />
      <Input
        placeholder="Valor"
        type="number"
        value={valor}
        onChange={(e) => setValor(e.target.value)}
      />

      <select
        className="border p-2 rounded"
        value={categoria}
        onChange={(e) => setCategoria(e.target.value)}
      >
        {categorias.map((c) => (
          <option key={c}>{c}</option>
        ))}
      </select>

      <div className="flex gap-2">
        <Button onClick={addEntrada}>+ Entrada</Button>
        <Button onClick={addGasto}>- Gasto</Button>
      </div>
    </CardContent>
  </Card>

  {/* CATEGORIAS */}
  <Card>
    <CardContent className="p-4">
      <h2 className="font-bold">Gastos por Categoria</h2>
      <ul>
        {gastoPorCategoria.map((c, i) => (
          <li key={i}>
            {c.cat}: R$ {c.total.toFixed(2)}
          </li>
        ))}
      </ul>
    </CardContent>
  </Card>

  {/* HISTÓRICO */}
  <Card>
    <CardContent className="p-4">
      <h2 className="font-bold">Histórico</h2>
      <ul>
        {entradas.map((e, i) => (
          <li key={i}>
            + R$ {e.valor} - {e.desc}
          </li>
        ))}
        {gastos.map((g, i) => (
          <li key={i}>
            - R$ {g.valor} ({g.categoria}) - {g.desc}
          </li>
        ))}
      </ul>
    </CardContent>
  </Card>
</div>



);
}

