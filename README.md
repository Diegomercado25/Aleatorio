import React, { useState } from "react";
import { Card, CardContent } from "@/components/ui/card";
import { Button } from "@/components/ui/button";
import axios from "axios";

const LANGUAGES = ["JavaScript", "Python", "Java", "C++", "TypeScript", "Go", "Ruby"];

export default function GitHubRepoRandomizer() {
  const [language, setLanguage] = useState("");
  const [repo, setRepo] = useState(null);
  const [loading, setLoading] = useState(false);

  const fetchRandomRepo = async () => {
    if (!language) return;
    setLoading(true);
    try {
      const response = await axios.get(`https://api.github.com/search/repositories`, {
        params: {
          q: `language:${language}`,
          sort: "stars",
          order: "desc",
          per_page: 100
        }
      });
      const repos = response.data.items;
      if (repos.length > 0) {
        const randomRepo = repos[Math.floor(Math.random() * repos.length)];
        setRepo(randomRepo);
      } else {
        setRepo(null);
      }
    } catch (error) {
      console.error("Error fetching repositories:", error);
      setRepo(null);
    }
    setLoading(false);
  };

  return (
    <div className="max-w-xl mx-auto mt-10 p-4">
      <h1 className="text-2xl font-bold mb-4">Buscador Aleatorio de Repositorios de GitHub</h1>
      <div className="flex gap-2 mb-4">
        <select
          value={language}
          onChange={(e) => setLanguage(e.target.value)}
          className="p-2 border rounded w-full"
        >
          <option value="">Selecciona un lenguaje</option>
          {LANGUAGES.map((lang) => (
            <option key={lang} value={lang}>{lang}</option>
          ))}
        </select>
        <Button onClick={fetchRandomRepo} disabled={loading || !language}>
          {loading ? "Buscando..." : "Obtener repositorio"}
        </Button>
      </div>

      {repo && (
        <Card className="mt-4">
          <CardContent className="p-4">
            <h2 className="text-xl font-semibold mb-2">
              <a href={repo.html_url} target="_blank" rel="noopener noreferrer" className="text-blue-600 hover:underline">
                {repo.full_name}
              </a>
            </h2>
            <p className="mb-2">{repo.description || "Sin descripción"}</p>
            <ul className="text-sm text-gray-700">
              <li><strong>⭐ Estrellas:</strong> {repo.stargazers_count}</li>
              <li><strong>🔀 Bifurcaciones:</strong> {repo.forks_count}</li>
              <li><strong>🚨 Incidencias abiertas:</strong> {repo.open_issues_count}</li>
            </ul>
          </CardContent>
        </Card>
      )}
    </div>
  );
}
