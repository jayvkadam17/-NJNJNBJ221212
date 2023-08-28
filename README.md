import { useState, useEffect } from 'react';

function useDebounce(value, delay) {
  const [debouncedValue, setDebouncedValue] = useState(value);

  useEffect(() => {
    const timer = setTimeout(() => {
      setDebouncedValue(value);
    }, delay);

    return () => {
      clearTimeout(timer);
    };
  }, [value, delay]);

  return debouncedValue;
}

export default useDebounce;
import React, { useState } from 'react';
import useDebounce from './useDebounce';

function App() {
  const [inputValue, setInputValue] = useState('');
  const debouncedInputValue = useDebounce(inputValue, 300); // Adjust the delay as needed
  const [avatarUrl, setAvatarUrl] = useState('');

  useEffect(() => {
    if (debouncedInputValue) {
      fetch(`https://api.github.com/users/${debouncedInputValue}`)
        .then(response => response.json())
        .then(data => {
          if (data.avatar_url) {
            setAvatarUrl(data.avatar_url);
          }
        })
        .catch(error => {
          console.error('Error fetching avatar:', error);
          setAvatarUrl('');
        });
    } else {
      setAvatarUrl('');
    }
  }, [debouncedInputValue]);

  return (
    <div>
      <h1>GitHub User Avatar Finder</h1>
      <input
        type="text"
        placeholder="Enter GitHub username"
        value={inputValue}
        onChange={e => setInputValue(e.target.value)}
      />
      {avatarUrl ? <img src={avatarUrl} alt="User Avatar" /> : null}
    </div>
  );
}

export default App;
