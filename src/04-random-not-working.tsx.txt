import React from 'react';
import './App.css';
const { useEffect, useRef, useState, useCallback } = React;

function App() {
  const [counter, setCounter] = useState(0);
  const [isRunning, setIsRunning] = useState(false);

  const shuffle = (raw: number[]) => {
    const array = [...raw];
    for (let i = array.length - 1; i > 0; i--) {
      let j = Math.floor(Math.random() * (i + 1));
      [array[i], array[j]] = [array[j], array[i]];
    }
    return array;
  };
  // 0~99までのシャッフルされた配列
  const shuffledArray = shuffle(Array.from({ length: 100 }, (_, i) => i));

  const useAnimationFrame = (isRunning: boolean, callback = () => { }) => {
    const reqIdRef = useRef<number>();
    const loop = useCallback(() => {
      if (isRunning) {
        reqIdRef.current = requestAnimationFrame(loop);
        callback();
      }
    }, [isRunning, callback]);

    useEffect(() => {
      reqIdRef.current = requestAnimationFrame(loop);
      let currentnum = 0;
      if (reqIdRef.current) currentnum = reqIdRef.current;
      return () => cancelAnimationFrame(currentnum);
    }, [loop]);
  };


  // setCounter するたびに関数を再生成するのを防ぐ
  const countUp = useCallback(() => {
    setCounter(prevCount => ++prevCount);
  }, []);

  useAnimationFrame(isRunning, countUp);

  return (
    <div className="App">
      <div>{counter === 0 ? "??" : shuffledArray[counter % 100]}</div>
      <button onClick={() => setIsRunning(true)}>START</button>
      <button onClick={() => setIsRunning(false)}>STOP</button>
    </div>
  );
}

export default App;
