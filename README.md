# vercel-new-testbfhl-frontend
import React, { useState } from 'react';

const App = () => {
  const [inputJson, setInputJson] = useState('');
  const [response, setResponse] = useState(null);
  const [selectedOptions, setSelectedOptions] = useState([]);

  const handleSubmit = async (event) => {
    event.preventDefault();
    try {
      const jsonInput = JSON.parse(inputJson);
      const response = await fetch('/api/bfhl', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(jsonInput),
      });
      const responseData = await response.json();
      setResponse(responseData);
    } catch (error) {
      console.error(error);
      alert('Invalid JSON input');
    }
  };

  const handleSelectChange = (event) => {
    const selectedOptions = Array.from(event.target.selectedOptions, (option) => option.value);
    setSelectedOptions(selectedOptions);
  };

  const renderResponse = () => {
    if (!response) return null;
    const filteredResponse = {};
    selectedOptions.forEach((option) => {
      if (option === 'Alphabets') {
        filteredResponse.alphabets = response.alphabets;
      } else if (option === 'Numbers') {
        filteredResponse.numbers = response.numbers;
      } else if (option === 'Highest lowercase alphabet') {
        filteredResponse.highest_lowercase_alphabet = response.highest_lowercase_alphabet;
      }
    });
    return (
      <div>
        {Object.keys(filteredResponse).map((key) => (
          <div key={key}>
            <h2>{key}</h2>
            <ul>
              {filteredResponse[key].map((item) => (
                <li key={item}>{item}</li>
              ))}
            </ul>
          </div>
        ))}
      </div>
    );
  };

  return (
    <div>
      <h1>{process.env.ROLL_NUMBER}</h1>
      <form onSubmit={handleSubmit}>
        <textarea value={inputJson} onChange={(event) => setInputJson(event.target.value)} />
        <button type="submit">Submit</button>
      </form>
      {response && (
        <div>
          <select multiple value={selectedOptions} onChange={handleSelectChange}>
            <option value="Alphabets">Alphabets</option>
            <option value="Numbers">Numbers</option>
            <option value="Highest lowercase alphabet">Highest lowercase alphabet</option>
          </select>
          {renderResponse()}
        </div>
      )}
    </div>
  );
};

export default App;
