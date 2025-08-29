import React, { useState } from "react";
import { Button } from "@/components/ui/button";
import { Card, CardContent } from "@/components/ui/card";

export default function GeneFinder() {
  const [genome, setGenome] = useState("");
  const [query, setQuery] = useState("");
  const [results, setResults] = useState([]);
  const [summary, setSummary] = useState(null);

  const handleGenomeUpload = async (e) => {
    const file = e.target.files[0];
    if (!file) return;
    const text = await file.text();
    setGenome(text.replace(/>.*\n/g, "").replace(/\s+/g, ""));
  };

  const handleSearch = () => {
    if (!genome || !query) return;
    const g = genome.toUpperCase();
    const q = query.toUpperCase();
    const resultsArr = [];

    for (let i = 0; i <= g.length - q.length; i++) {
      let mismatches = 0;
      for (let j = 0; j < q.length; j++) {
        if (g[i + j] !== q[j]) mismatches++;
      }
      const identity = ((q.length - mismatches) / q.length) * 100;
      const coverage = (q.length / q.length) * 100; // full query length always compared
      if (identity >= 80) {
        resultsArr.push({ position: i + 1, match: identity.toFixed(2), coverage: coverage.toFixed(2) });
      }
    }

    // Calculate summary
    let bestHit = null;
    let avgMatch = 0;
    let avgCoverage = 0;
    if (resultsArr.length > 0) {
      bestHit = resultsArr.reduce((a, b) => (parseFloat(a.match) > parseFloat(b.match) ? a : b));
      avgMatch = resultsArr.reduce((acc, r) => acc + parseFloat(r.match), 0) / resultsArr.length;
      avgCoverage = resultsArr.reduce((acc, r) => acc + parseFloat(r.coverage), 0) / resultsArr.length;
    }

    setResults(resultsArr);
    setSummary({ bestHit, avgMatch: avgMatch.toFixed(2), avgCoverage: avgCoverage.toFixed(2) });
  };

  const handleDownloadCSV = () => {
    let csvContent = "data:text/csv;charset=utf-8,";
    csvContent += "Summary\\n";
    if (summary) {
      csvContent += `Best Hit Match %,${summary.bestHit ? summary.bestHit.match : "N/A"},Best Hit Coverage %,${summary.bestHit ? summary.bestHit.coverage : "N/A"}\\n`;
      csvContent += `Average Match %,${summary.avgMatch},Average Coverage %,${summary.avgCoverage}\\n`;
    }
    csvContent += "\\nDetailed Results\\nPosition,% Match,% Query Coverage\\n";
    results.forEach((r) => {
      csvContent += `${r.position},${r.match},${r.coverage}\\n`;
    });

    const encodedUri = encodeURI(csvContent);
    const link = document.createElement("a");
    link.setAttribute("href", encodedUri);
    link.setAttribute("download", "gene_finder_results.csv");
    document.body.appendChild(link);
    link.click();
    document.body.removeChild(link);
  };

  return (
    <div className="p-6 max-w-4xl mx-auto">
      <h1 className="text-2xl font-bold mb-4">Gene Finder 2.0</h1>
      <Card className="mb-4">
        <CardContent>
          <p className="mb-2 font-semibold">Upload Whole Genome (FASTA up to 1GB)</p>
          <input type="file" accept=".fasta,.fa,.txt" onChange={handleGenomeUpload} />
        </CardContent>
      </Card>

      <Card className="mb-4">
        <CardContent>
          <p className="mb-2 font-semibold">Paste Query Sequence (≤7000 bp)</p>
          <textarea
            className="w-full p-2 border rounded"
            rows={6}
            value={query}
            onChange={(e) => setQuery(e.target.value)}
          />
        </CardContent>
      </Card>

      <Button className="mb-4" onClick={handleSearch}>
        Search
      </Button>

      {summary && (
        <Card className="mb-4">
          <CardContent>
            <h2 className="text-lg font-semibold mb-2">Summary</h2>
            <p><strong>Best Hit:</strong> % Match {summary.bestHit ? summary.bestHit.match : "N/A"}, % Coverage {summary.bestHit ? summary.bestHit.coverage : "N/A"}</p>
            <p><strong>Average % Match:</strong> {summary.avgMatch}</p>
            <p><strong>Average % Coverage:</strong> {summary.avgCoverage}</p>
          </CardContent>
        </Card>
      )}

      {results.length > 0 && (
        <Card className="mb-4">
          <CardContent>
            <h2 className="text-lg font-semibold mb-2">Results</h2>
            <table className="w-full border">
              <thead>
                <tr>
                  <th className="border px-2 py-1">Position</th>
                  <th className="border px-2 py-1">% Match</th>
                  <th className="border px-2 py-1">% Query Coverage</th>
                </tr>
              </thead>
              <tbody>
                {results.map((r, i) => (
                  <tr key={i}>
                    <td className="border px-2 py-1">{r.position}</td>
                    <td className="border px-2 py-1">{r.match}</td>
                    <td className="border px-2 py-1">{r.coverage}</td>
                  </tr>
                ))}
              </tbody>
            </table>
            <Button className="mt-4" onClick={handleDownloadCSV}>Download CSV</Button>
          </CardContent>
        </Card>
      )}
    </div>
  );
}
