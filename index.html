<!DOCTYPE html>
<html lang="de">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Polar H10 Heart Rate Monitor</title>
  <script crossorigin src="https://unpkg.com/react@18/umd/react.production.min.js"></script>
  <script crossorigin src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js"></script>
  <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
  <script src="https://cdn.tailwindcss.com"></script>
  <style>
    body { margin: 0; padding: 0; }
  </style>
</head>
<body>
  <div id="root"></div>

  <script type="text/babel">
    const { useState, useEffect, useRef } = React;

    // Lucide Icons als einfache SVGs
    const Heart = ({ className }) => (
      <svg className={className} fill="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
        <path d="M20.84 4.61a5.5 5.5 0 0 0-7.78 0L12 5.67l-1.06-1.06a5.5 5.5 0 0 0-7.78 7.78l1.06 1.06L12 21.23l7.78-7.78 1.06-1.06a5.5 5.5 0 0 0 0-7.78z"/>
      </svg>
    );

    const Bluetooth = ({ className }) => (
      <svg className={className} fill="none" stroke="currentColor" strokeWidth="2" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
        <path d="m7 7 10 10-5 5V2l5 5L7 17"/>
      </svg>
    );

    const Activity = ({ className }) => (
      <svg className={className} fill="none" stroke="currentColor" strokeWidth="2" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
        <path d="M22 12h-4l-3 9L9 3l-3 9H2"/>
      </svg>
    );

    function HeartRateMonitor() {
      const [connected, setConnected] = useState(false);
      const [heartRate, setHeartRate] = useState(0);
      const [history, setHistory] = useState([]);
      const [avgHR, setAvgHR] = useState(0);
      const [minHR, setMinHR] = useState(0);
      const [maxHR, setMaxHR] = useState(0);
      const [soundEnabled, setSoundEnabled] = useState(true);
      const [debugLog, setDebugLog] = useState([]);
      
      const canvasRef = useRef(null);
      const deviceRef = useRef(null);
      const characteristicRef = useRef(null);
      const audioContextRef = useRef(null);
      const animationRef = useRef(null);
      const trailPointsRef = useRef([]);
      const lastBeepTimeRef = useRef(Date.now());
      const beepAnimationFrameRef = useRef(null);
      const scrollXRef = useRef(0);
      const previousScrollXRef = useRef(0);

      const addLog = (message) => {
        console.log(message);
        setDebugLog(prev => [...prev, `${new Date().toLocaleTimeString()}: ${message}`].slice(-10));
      };

      useEffect(() => {
        return () => {
          if (audioContextRef.current) {
            audioContextRef.current.close();
          }
          if (beepAnimationFrameRef.current) {
            cancelAnimationFrame(beepAnimationFrameRef.current);
          }
        };
      }, []);

      const playBeep = () => {
        if (!soundEnabled) return;

        try {
          if (!audioContextRef.current) {
            audioContextRef.current = new (window.AudioContext || window.webkitAudioContext)();
          }

          const ctx = audioContextRef.current;
          if (ctx.state === 'suspended') {
            ctx.resume();
          }

          const now = ctx.currentTime;
          
          const osc = ctx.createOscillator();
          const gain = ctx.createGain();
          
          osc.connect(gain);
          gain.connect(ctx.destination);
          
          osc.frequency.value = 800;
          osc.type = 'square';
          
          gain.gain.setValueAtTime(0, now);
          gain.gain.linearRampToValueAtTime(0.3, now + 0.002);
          gain.gain.exponentialRampToValueAtTime(0.001, now + 0.025);
          
          osc.start(now);
          osc.stop(now + 0.025);
          
          lastBeepTimeRef.current = Date.now();
          
        } catch (e) {
          console.error('Audio error:', e);
        }
      };

      // Kontinuierlicher Beep-Loop mit dynamischer Anpassung
      useEffect(() => {
        if (!connected || heartRate === 0 || !soundEnabled) {
          if (beepAnimationFrameRef.current) {
            cancelAnimationFrame(beepAnimationFrameRef.current);
            beepAnimationFrameRef.current = null;
          }
          return;
        }

        const beepLoop = () => {
          const now = Date.now();
          const timeSinceLastBeep = now - lastBeepTimeRef.current;
          const beatInterval = 60000 / heartRate;
          
          if (timeSinceLastBeep >= beatInterval) {
            playBeep();
            addLog(`💓 BEEP bei ${heartRate} BPM`);
          }
          
          beepAnimationFrameRef.current = requestAnimationFrame(beepLoop);
        };

        beepLoop();

        return () => {
          if (beepAnimationFrameRef.current) {
            cancelAnimationFrame(beepAnimationFrameRef.current);
          }
        };
      }, [heartRate, connected, soundEnabled]);

      const connectToDevice = async () => {
        try {
          addLog('Starte Verbindung...');
          
          if (!navigator.bluetooth) {
            addLog('FEHLER: Web Bluetooth nicht verfügbar');
            alert('Web Bluetooth wird nicht unterstützt! Bitte Chrome oder Edge verwenden.');
            return;
          }

          addLog('Öffne Geräteauswahl...');
          
          const device = await navigator.bluetooth.requestDevice({
            filters: [{ services: ['heart_rate'] }],
            optionalServices: ['heart_rate']
          });

          addLog(`Gerät ausgewählt: ${device.name || device.id}`);
          deviceRef.current = device;
          
          device.addEventListener('gattserverdisconnected', () => {
            addLog('Gerät getrennt');
            setConnected(false);
            setHeartRate(0);
            setHistory([]);
            trailPointsRef.current = [];
          });

          addLog('Verbinde mit GATT Server...');
          const server = await device.gatt.connect();
          addLog('GATT Server verbunden');
          
          addLog('Suche Heart Rate Service...');
          const service = await server.getPrimaryService('heart_rate');
          addLog('Heart Rate Service gefunden');
          
          addLog('Suche Heart Rate Measurement Characteristic...');
          const characteristic = await service.getCharacteristic('heart_rate_measurement');
          addLog('Characteristic gefunden');
          
          characteristicRef.current = characteristic;
          
          addLog('Starte Notifications...');
          await characteristic.startNotifications();
          characteristic.addEventListener('characteristicvaluechanged', handleHeartRateChange);
          
          addLog('✓ ERFOLGREICH VERBUNDEN!');
          setConnected(true);
          lastBeepTimeRef.current = Date.now();
        } catch (error) {
          addLog(`FEHLER: ${error.name} - ${error.message}`);
          console.error('Verbindungsfehler:', error);
          alert('Verbindung fehlgeschlagen: ' + error.message);
        }
      };

      const handleHeartRateChange = (event) => {
        const value = event.target.value;
        const flags = value.getUint8(0);
        let hr;
        
        if ((flags & 0x01) === 0) {
          hr = value.getUint8(1);
        } else {
          hr = value.getUint16(1, true);
        }
        
        addLog(`💓 Herzfrequenz: ${hr} BPM`);
        setHeartRate(hr);
        
        setHistory(prev => {
          const newHistory = [...prev, hr].slice(-60);
          const sum = newHistory.reduce((a, b) => a + b, 0);
          const avg = Math.round(sum / newHistory.length);
          const min = Math.min(...newHistory);
          const max = Math.max(...newHistory);
          
          setAvgHR(avg);
          setMinHR(min);
          setMaxHR(max);
          
          return newHistory;
        });
      };

      const getPulseHeight = (timeSinceLastBeat, beatInterval) => {
        if (beatInterval === 0) return 0;
        
        const phase = timeSinceLastBeat / beatInterval;
        
        // Spike: kurz runter, dann hoch rauf
        if (phase < 0.03) {
          const t = phase / 0.03;
          return -0.3 * Math.sin(t * Math.PI);
        } else if (phase < 0.08) {
          const t = (phase - 0.03) / 0.05;
          return Math.sin(t * Math.PI) * 1.0;
        } else {
          return 0;
        }
      };

      useEffect(() => {
        const canvas = canvasRef.current;
        if (!canvas) return;

        const ctx = canvas.getContext('2d');
        const width = canvas.width;
        const height = canvas.height;
        const centerY = height / 2;

        const animate = () => {
          // Komplett schwarz
          ctx.fillStyle = '#000000';
          ctx.fillRect(0, 0, width, height);

          // SEHR dezentes Gitter
          ctx.strokeStyle = '#0a0a0a';
          ctx.lineWidth = 0.5;
          
          for (let x = 0; x < width; x += 50) {
            ctx.beginPath();
            ctx.moveTo(x, 0);
            ctx.lineTo(x, height);
            ctx.stroke();
          }
          
          for (let y = 0; y < height; y += 50) {
            ctx.beginPath();
            ctx.moveTo(0, y);
            ctx.lineTo(width, y);
            ctx.stroke();
          }

          // Mittellinie - kaum sichtbar
          ctx.strokeStyle = '#0d0d0d';
          ctx.lineWidth = 1;
          ctx.beginPath();
          ctx.moveTo(0, centerY);
          ctx.lineTo(width, centerY);
          ctx.stroke();

          // KONSTANTE Scrollgeschwindigkeit
          previousScrollXRef.current = scrollXRef.current;
          scrollXRef.current += 3;
          
          // Wenn Punkt am Rand ankommt, Trail clearen
          if (scrollXRef.current > width) {
            scrollXRef.current = 0;
            trailPointsRef.current = [];
          }

          // Berechne aktuelle Pulse-Position
          const now = Date.now();
          const timeSinceLastBeat = now - lastBeepTimeRef.current;
          const beatInterval = heartRate > 0 ? (60000 / heartRate) : 0;
          
          const pulseHeight = getPulseHeight(timeSinceLastBeat, beatInterval);
          const y = centerY - pulseHeight * 80;

          // Punkt zum Trail hinzufügen
          trailPointsRef.current.push({ x: scrollXRef.current, y: y });
          
          // Trail-Länge begrenzen
          const maxTrailLength = Math.floor(width / 3);
          while (trailPointsRef.current.length > maxTrailLength) {
            trailPointsRef.current.shift();
          }

          // Trail zeichnen mit Fadeout-Effekt
          if (trailPointsRef.current.length > 1) {
            for (let i = 0; i < trailPointsRef.current.length - 1; i++) {
              const point = trailPointsRef.current[i];
              const nextPoint = trailPointsRef.current[i + 1];
              
              // Berechne Entfernung vom aktuellen Punkt
              const distanceFromCurrent = scrollXRef.current - point.x;
              
              // Opacity basierend auf Entfernung
              const maxDistance = width / 3;
              const opacity = Math.max(0, 1 - (distanceFromCurrent / maxDistance));
              
              ctx.strokeStyle = `rgba(0, 255, 0, ${opacity})`;
              ctx.lineWidth = 3;
              ctx.shadowBlur = 8 * opacity;
              ctx.shadowColor = `rgba(0, 255, 0, ${opacity})`;
              
              ctx.beginPath();
              ctx.moveTo(point.x, point.y);
              ctx.lineTo(nextPoint.x, nextPoint.y);
              ctx.stroke();
            }
          }

          // Aktueller Punkt (leuchtend)
          ctx.shadowBlur = 20;
          ctx.shadowColor = '#00ff00';
          ctx.fillStyle = '#00ff00';
          
          ctx.beginPath();
          ctx.arc(scrollXRef.current, y, 5, 0, Math.PI * 2);
          ctx.fill();

          animationRef.current = requestAnimationFrame(animate);
        };

        animate();

        return () => {
          if (animationRef.current) {
            cancelAnimationFrame(animationRef.current);
          }
        };
      }, [heartRate, connected]);

      const disconnect = () => {
        addLog('Trenne Verbindung...');
        
        if (beepAnimationFrameRef.current) {
          cancelAnimationFrame(beepAnimationFrameRef.current);
        }
        
        if (characteristicRef.current) {
          try {
            characteristicRef.current.stopNotifications();
            addLog('Notifications gestoppt');
          } catch (e) {
            console.error('Stop notifications error:', e);
          }
        }
        
        if (deviceRef.current && deviceRef.current.gatt.connected) {
          deviceRef.current.gatt.disconnect();
          addLog('GATT getrennt');
        }
        
        setConnected(false);
        setHeartRate(0);
        setHistory([]);
        setAvgHR(0);
        setMinHR(0);
        setMaxHR(0);
        trailPointsRef.current = [];
        addLog('Verbindung getrennt');
      };

      return (
        <div className="min-h-screen bg-black text-green-500 font-mono p-4 flex flex-col">
          <div className="max-w-7xl mx-auto w-full flex-1 flex flex-col">
            <div className="flex items-center justify-between mb-4 border-b-2 border-green-500 pb-3">
              <div className="flex items-center gap-3">
                <Activity className="w-8 h-8" />
                <div>
                  <h1 className="text-2xl font-bold">CARDIAC MONITOR v4.2</h1>
                  <p className="text-sm text-green-400">Polar H10 Interface</p>
                </div>
              </div>
              
              {!connected ? (
                <button
                  onClick={connectToDevice}
                  className="flex items-center gap-2 bg-green-900 hover:bg-green-800 px-6 py-3 rounded border-2 border-green-500 transition-colors cursor-pointer"
                >
                  <Bluetooth className="w-5 h-5" />
                  CONNECT DEVICE
                </button>
              ) : (
                <button
                  onClick={disconnect}
                  className="flex items-center gap-2 bg-red-900 hover:bg-red-800 px-6 py-3 rounded border-2 border-red-500 text-red-500 transition-colors cursor-pointer"
                >
                  DISCONNECT
                </button>
              )}
            </div>

            <div className="mb-3 flex items-center gap-4">
              <div className="flex items-center gap-2">
                <div className={connected ? "w-3 h-3 rounded-full bg-green-500 animate-pulse" : "w-3 h-3 rounded-full bg-gray-700"} />
                <span className="text-sm">{connected ? "CONNECTED" : "DISCONNECTED"}</span>
              </div>
              
              <button
                onClick={() => setSoundEnabled(!soundEnabled)}
                className="text-sm px-3 py-1 border border-green-500 rounded hover:bg-green-900 transition-colors cursor-pointer"
              >
                SOUND: {soundEnabled ? "ON" : "OFF"}
              </button>
            </div>

            {debugLog.length > 0 && (
              <div className="mb-3 bg-gray-900 border border-yellow-500 p-2 rounded text-xs text-yellow-400 max-h-32 overflow-y-auto">
                <div className="font-bold mb-1">DEBUG LOG:</div>
                {debugLog.map((log, i) => (
                  <div key={i}>{log}</div>
                ))}
              </div>
            )}

            <div className="grid grid-cols-1 md:grid-cols-4 gap-3 mb-3">
              <div className="md:col-span-3 bg-gray-900 border-2 border-green-500 p-4 rounded">
                <div className="text-xs mb-1">HEART RATE</div>
                <div className="flex items-baseline gap-2">
                  <div className="text-6xl font-bold tabular-nums">
                    {heartRate || '--'}
                  </div>
                  <div className="text-2xl mb-1">BPM</div>
                  {connected && heartRate > 0 && (
                    <Heart className="w-10 h-10 ml-3 text-red-500 animate-pulse" />
                  )}
                </div>
              </div>

              <div className="bg-gray-900 border-2 border-green-500 p-4 rounded space-y-3">
                <div>
                  <div className="text-xs text-green-400">AVG</div>
                  <div className="text-2xl font-bold">{avgHR || '--'}</div>
                </div>
                <div>
                  <div className="text-xs text-green-400">MIN</div>
                  <div className="text-2xl font-bold">{minHR || '--'}</div>
                </div>
                <div>
                  <div className="text-xs text-green-400">MAX</div>
                  <div className="text-2xl font-bold">{maxHR || '--'}</div>
                </div>
              </div>
            </div>

            <div className="bg-gray-900 border-2 border-green-500 rounded p-3 flex-1 flex flex-col min-h-0">
              <div className="text-xs mb-2">PULSE VISUALIZATION</div>
              <div className="flex-1 min-h-0">
                <canvas
                  ref={canvasRef}
                  width={1800}
                  height={500}
                  className="w-full h-full border border-green-700 rounded"
                />
              </div>
              <div className="text-xs text-green-400 mt-2">
                {connected ? 'MONITORING...' : 'NO SIGNAL'}
              </div>
            </div>

            {!connected && (
              <div className="mt-3 bg-gray-900 border border-green-700 p-3 rounded text-xs">
                <p className="mb-1">📡 ANWEISUNGEN:</p>
                <ul className="space-y-1 text-green-400">
                  <li>1. Stelle sicher, dass dein Polar H10 eingeschaltet ist (Hautkontakt)</li>
                  <li>2. Aktiviere Bluetooth an deinem Computer</li>
                  <li>3. Verwende Chrome oder Edge Browser</li>
                  <li>4. Klicke auf "CONNECT DEVICE"</li>
                  <li>5. Wähle deinen Polar H10 aus der Liste</li>
                </ul>
              </div>
            )}
          </div>
        </div>
      );
    }

    ReactDOM.render(<HeartRateMonitor />, document.getElementById('root'));
  </script>
</body>
</html>
