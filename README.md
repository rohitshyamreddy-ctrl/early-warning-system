# AI Early Warning Health Monitor

## 🚀 Project Overview
This project is an AI-based early warning system that monitors:
- Heart Rate
- SpO₂ (Oxygen Saturation)

Unlike traditional systems that trigger alerts only after thresholds are crossed, our system detects **gradual trends** to predict early deterioration.

---

## 🧠 Key Idea
We use **Linear Regression** to analyze trends:
- Increasing Heart Rate → Possible cardiac stress
- Decreasing SpO₂ → Possible respiratory issue
- Combined trends → Early warning for critical conditions

---

## ⚙️ Features
- Real-time monitoring
- Trend-based AI analysis
- Early risk prediction
- Graph visualization
- Alert system (Low / Moderate / High risk)

---

## 📊 Tech Stack
- Python
- Node.js
- Css

---
Code:
import { useEffect, useState } from "react";
import './App.css';
import { Line } from "react-chartjs-2";
import {
  Chart as ChartJS,
  LineElement,
  CategoryScale,
  LinearScale,
  PointElement
} from "chart.js";

ChartJS.register(LineElement, CategoryScale, LinearScale, PointElement);

function App() {
  const [data, setData] = useState(null);
  const [hrData, setHrData] = useState([]);
  const [spo2Data, setSpo2Data] = useState([]);
  const [labels, setLabels] = useState([]);

  useEffect(() => {
    const interval = setInterval(async () => {
      const res = await fetch("http://127.0.0.1:8000/vitals");
      const result = await res.json();

      setData(result);

      setHrData(prev => [...prev.slice(-10), result.heart_rate]);
      setSpo2Data(prev => [...prev.slice(-10), result.spo2]);
      setLabels(prev => [...prev.slice(-10), result.time]);

    }, 1000);

    return () => clearInterval(interval);
  }, []);

  const chartData = {
    labels: labels,
    datasets: [
      {
        label: "Heart Rate",
        data: hrData,
        borderColor: "red",
        tension: 0.4
      },
      {
        label: "SpO2",
        data: spo2Data,
        borderColor: "blue",
        tension: 0.4
      }
    ]
  };

  
    return (
  <div style={{
    textAlign: "center",
    padding: "20px",
    background: "#0b0f1a",
    color: "white",
    minHeight: "100vh"
  }}>

    <h1>🫀 Smart ICU Dashboard</h1>

    {/* VITAL BOXES */}
    {data && (
      <div style={{
        display: "flex",
        justifyContent: "space-around",
        flexWrap: "wrap",
        marginTop: "30px"
      }}>

        {/* HEART RATE BOX */}
        <div style={{
          border: "2px solid red",
          borderRadius: "12px",
          padding: "20px",
          width: "200px",
          background: "#111827",
          margin: "10px"
        }}>
          <h3>Heart Rate</h3>
          <h1 style={{ color: "red" }}>{data.heart_rate}</h1>
          <p>bpm</p>
        </div>

        {/* SPO2 BOX */}
        <div style={{
          border: "2px solid cyan",
          borderRadius: "12px",
          padding: "20px",
          width: "200px",
          background: "#111827",
          margin: "10px"
        }}>
          <h3>SpO2</h3>
          <h1 style={{ color: "cyan" }}>{data.spo2}</h1>
          <p>%</p>
        </div>

        {/* STATUS BOX */}
        <div style={{
          border: "2px solid white",
          borderRadius: "12px",
          padding: "20px",
          width: "200px",
          background: "#111827",
          margin: "10px"
        }}>
          <h3>Status</h3>
          <h1 style={{
            color:
              data.status === "emergency" ? "red" :
              data.status === "critical" ? "orange" :
              "lightgreen"
          }}>
            {data.status}
          </h1>
        </div>

      </div>
    )}

    {/* GRAPH BOX */}
    <div style={{
      marginTop: "40px",
      padding: "20px",
      border: "2px solid white",
      borderRadius: "12px",
      background: "#111827",
      width: "85%",
      marginLeft: "auto",
      marginRight: "auto"
    }}>
      <h2>Vitals Trend</h2>
      <Line data={chartData} />
    </div>

  </div>
);
}

export default App;
