# beni-oku// AI Destekli Kişisel Gelişim Uygulaması (React + Tailwind)

import React, { useReducer, useEffect } from "react";
import { Card, CardContent } from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import { Input } from "@/components/ui/input";
import { Progress } from "@/components/ui/progress";
import { motion } from "framer-motion";

const initialState = {
  goal: "",
  response: "",
  progress: 0,
  loading: false,
};

function reducer(state, action) {
  switch (action.type) {
    case "SET_GOAL":
      return { ...state, goal: action.payload };
    case "LOADING":
      return { ...state, loading: true, progress: 10, response: "" };
    case "DONE":
      return { ...state, response: action.payload, progress: 100, loading: false };
    case "UPDATE_PROGRESS":
      return { ...state, progress: action.payload };
    default:
      return state;
  }
}

function formatResponse(goal) {
  return `🎯 Hedefin: ${goal}\n\n1. Hedefini parçalara ayır.\n2. Günlük 15 dakikanı bu hedefe ayır.\n3. Haftalık değerlendirme yap.\n4. Gelişimini not al.`;
}

export default function AIGrowthApp() {
  const [state, dispatch] = useReducer(reducer, initialState);
  const { goal, response, progress, loading } = state;

  useEffect(() => {
    if (loading && progress < 90) {
      const interval = setInterval(() => {
        dispatch({ type: "UPDATE_PROGRESS", payload: progress + 10 });
      }, 200);
      return () => clearInterval(interval);
    }
  }, [loading, progress]);

  const handleGenerate = () => {
    if (!goal) return;
    dispatch({ type: "LOADING" });
    const result = formatResponse(goal);
    dispatch({ type: "DONE", payload: result });
  };

  return (
    <div className="max-w-2xl mx-auto p-4 space-y-4">
      <h1 className="text-3xl font-bold text-center">🧠 AI Kişisel Gelişim Koçu</h1>

      <Card className="shadow-xl">
        <CardContent className="space-y-4">
          <Input
            placeholder="Hedefini yaz (örnek: Günde 1 kitap bitirmek)"
            value={goal}
            onChange={(e) => dispatch({ type: "SET_GOAL", payload: e.target.value })}
          />
          {!goal && <p className="text-red-500 text-sm">Lütfen bir hedef gir.</p>}
          <Button onClick={handleGenerate} disabled={loading || !goal}>
            {loading ? "Yükleniyor..." : "AI Plan Oluştur"}
          </Button>
          {progress > 0 && <Progress value={progress} />}
          {response && (
            <>
              <p className="text-green-600 text-sm">✅ Plan hazır! Uygulamaya başla.</p>
              <pre className="bg-gray-50 p-3 rounded text-sm whitespace-pre-wrap">
                {response}
              </pre>
            </>
          )}
        </CardContent>
      </Card>
    </div>
  );
}
