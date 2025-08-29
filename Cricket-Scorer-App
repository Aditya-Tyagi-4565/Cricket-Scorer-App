import React, { useState, useEffect, useCallback } from 'react';

// --- SVG Icons ---
const BatIcon = () => (
  <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" className="mr-2 h-5 w-5">
    <path d="M14.5 10.5 8 17l-1.5-1.5L13 9l1.5 1.5Z" />
    <path d="m14 14 4.5 4.5" />
    <path d="M2 22s5-5 10-10" />
    <path d="m18 8 4 4" />
  </svg>
);

const BallIcon = () => (
  <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24" fill="none" stroke="currentColor" strokeWidth="2" strokeLinecap="round" strokeLinejoin="round" className="mr-2 h-5 w-5">
    <circle cx="12" cy="12" r="10" />
  </svg>
);

// --- Main App Component ---
export default function App() {
  const [gameState, setGameState] = useState('setup'); // 'setup', 'playing', 'inning_break', 'finished'
  const [playersPerTeam, setPlayersPerTeam] = useState(11);
  const [team1, setTeam1] = useState({ name: 'Team A', players: Array(11).fill('').map((_, i) => `Player ${i + 1}`) });
  const [team2, setTeam2] = useState({ name: 'Team B', players: Array(11).fill('').map((_, i) => `Player ${i + 1}`) });
  const [tossWinner, setTossWinner] = useState('');
  const [decision, setDecision] = useState(''); // 'bat' or 'bowl'
  const [totalOvers, setTotalOvers] = useState(5);
  const [battingTeam, setBattingTeam] = useState(null);
  const [bowlingTeam, setBowlingTeam] = useState(null);
  const [addRunForWide, setAddRunForWide] = useState(true);
  const [addRunForNoBall, setAddRunForNoBall] = useState(true);

  const [score, setScore] = useState(0);
  const [wickets, setWickets] = useState(0);
  const [overs, setOvers] = useState(0);
  const [balls, setBalls] = useState(0);
  
  const [batsmen, setBatsmen] = useState([]);
  const [bowlers, setBowlers] = useState([]);
  const [firstInningsSummary, setFirstInningsSummary] = useState(null);

  const [striker, setStriker] = useState(null);
  const [nonStriker, setNonStriker] = useState(null);
  const [bowler, setBowler] = useState(null);

  const [commentary, setCommentary] = useState([]);
  const [inning, setInning] = useState(1);
  const [target, setTarget] = useState(0);

  const [event, setEvent] = useState(null); // '4', '6', 'W'

  // State for modals
  const [openingStriker, setOpeningStriker] = useState('');
  const [openingNonStriker, setOpeningNonStriker] = useState('');
  const [openingBowler, setOpeningBowler] = useState('');
  const [secondInningStriker, setSecondInningStriker] = useState('');
  const [secondInningNonStriker, setSecondInningNonStriker] = useState('');
  const [secondInningBowler, setSecondInningBowler] = useState('');
  const [showSecondInningSelector, setShowSecondInningSelector] = useState(false);
  const [isBowlerModalOpen, setIsBowlerModalOpen] = useState(false);
  const [isBatsmanModalOpen, setIsBatsmanModalOpen] = useState(false);
  const [isNoBallModalOpen, setIsNoBallModalOpen] = useState(false);
  const [noBallDetails, setNoBallDetails] = useState({ baseRun: 0 });

  // --- Utility Functions ---
  const formatOvers = (o, b) => `${o}.${b}`;
  const calculateStrikeRate = (runs, balls) => {
    if (balls === 0) return '0.00';
    return ((runs / balls) * 100).toFixed(2);
  };
  const calculateEconomy = (runs, balls) => {
    if (balls === 0) return '0.00';
    const oversBowled = Math.floor(balls / 6) + (balls % 6) / 6;
    return (runs / oversBowled).toFixed(2);
  };


  const showEvent = useCallback((type) => {
    setEvent(type);
    setTimeout(() => setEvent(null), 2000);
  }, []);

  // --- Game Logic ---
  const startMatch = () => {
    const firstBattingTeam = (tossWinner === team1.name && decision === 'bat') || (tossWinner === team2.name && decision === 'bowl') ? team1 : team2;
    const firstBowlingTeam = firstBattingTeam === team1 ? team2 : team1;

    setBattingTeam(firstBattingTeam);
    setBowlingTeam(firstBowlingTeam);

    const initialBatsmen = firstBattingTeam.players.map(p => ({ name: p, runs: 0, balls: 0, fours: 0, sixes: 0, status: 'not out' }));
    const initialBowlers = firstBowlingTeam.players.map(p => ({ name: p, overs: 0, balls: 0, runs: 0, wickets: 0 }));
    
    setBatsmen(initialBatsmen);
    setBowlers(initialBowlers);

    setStriker(initialBatsmen.find(p => p.name === openingStriker));
    setNonStriker(initialBatsmen.find(p => p.name === openingNonStriker));
    setBowler(initialBowlers.find(p => p.name === openingBowler));
    
    setGameState('playing');
    addCommentary(`Match starts! ${firstBattingTeam.name} is batting.`);
  };

  const addCommentary = (text) => {
    setCommentary(prev => [`${formatOvers(overs, balls)}: ${text}`, ...prev]);
  };

  const handleRun = (run) => {
    if (gameState !== 'playing') return;

    setScore(prev => prev + run);
    addCommentary(`${striker.name} scores ${run} run(s).`);
    if (run === 4) showEvent('4');
    if (run === 6) showEvent('6');

    setBowler(prev => ({ ...prev, runs: prev.runs + run }));
    setBowlers(prevBowlers => prevBowlers.map(b => 
        b.name === bowler.name ? { ...b, runs: b.runs + run } : b
    ));

    const newBatsmenList = batsmen.map(b => {
        if (b.name === striker.name) {
            return {
                ...b,
                runs: b.runs + run,
                balls: b.balls + 1,
                fours: b.fours + (run === 4 ? 1 : 0),
                sixes: b.sixes + (run === 6 ? 1 : 0),
            };
        }
        return b;
    });
    setBatsmen(newBatsmenList);

    const updatedStriker = newBatsmenList.find(b => b.name === striker.name);
    const updatedNonStriker = newBatsmenList.find(b => b.name === nonStriker.name);

    if (run % 2 !== 0) {
        setStriker(updatedNonStriker);
        setNonStriker(updatedStriker);
    } else {
        setStriker(updatedStriker);
    }
    
    endOfBall();
  };
  
  const handleExtra = (type) => {
    if (gameState !== 'playing') return;

    if (type === 'No Ball') {
        const baseRun = addRunForNoBall ? 1 : 0;
        setNoBallDetails({ baseRun });
        setIsNoBallModalOpen(true);
        return;
    }

    let runAdded = 0;
    let commentaryMsg = `Extra: ${type}.`;

    if (type === 'Wide') {
        if (addRunForWide) {
            runAdded = 1;
            commentaryMsg += ' 1 run added.';
        } else {
            commentaryMsg += ' No run added.';
        }
    } else { // For Byes and Leg Byes
        runAdded = 1;
        commentaryMsg += ' 1 run added.';
    }

    if (runAdded > 0) {
        setScore(prev => prev + runAdded);
        setBowler(prev => ({ ...prev, runs: prev.runs + runAdded }));
        setBowlers(bowlers.map(b => b.name === bowler.name ? { ...b, runs: b.runs + runAdded } : b));
    }

    addCommentary(commentaryMsg);

    if (type === 'Bye' || type === 'Leg Bye') {
        endOfBall();
    }
  };

  const handleNoBallRuns = (runsOffBat) => {
    const { baseRun } = noBallDetails;
    const totalRuns = baseRun + runsOffBat;

    addCommentary(`No Ball! ${baseRun} + ${runsOffBat} from the bat = ${totalRuns} run(s).`);
    if (runsOffBat === 4) showEvent('4');
    if (runsOffBat === 6) showEvent('6');

    setScore(prev => prev + totalRuns);

    setBowler(prev => ({ ...prev, runs: prev.runs + totalRuns }));
    setBowlers(prevBowlers => prevBowlers.map(b => 
        b.name === bowler.name ? { ...b, runs: b.runs + totalRuns } : b
    ));

    const newBatsmenList = batsmen.map(b => {
        if (b.name === striker.name) {
            return {
                ...b,
                runs: b.runs + runsOffBat,
                // No ball faced for no-ball
                fours: b.fours + (runsOffBat === 4 ? 1 : 0),
                sixes: b.sixes + (runsOffBat === 6 ? 1 : 0),
            };
        }
        return b;
    });
    setBatsmen(newBatsmenList);

    const updatedStriker = newBatsmenList.find(b => b.name === striker.name);
    const updatedNonStriker = newBatsmenList.find(b => b.name === nonStriker.name);

    if (runsOffBat % 2 !== 0) {
        setStriker(updatedNonStriker);
        setNonStriker(updatedStriker);
    } else {
        setStriker(updatedStriker);
    }

    setIsNoBallModalOpen(false);
  };

  const handleWicket = () => {
    if (gameState !== 'playing' || wickets >= playersPerTeam - 1) return;

    showEvent('W');
    const outBatsmanName = striker.name;
    addCommentary(`WICKET! ${outBatsmanName} is out.`);

    setWickets(prev => prev + 1);
    setBatsmen(prev => prev.map(b => b.name === outBatsmanName ? { ...b, status: 'out', balls: b.balls + 1 } : b));
    setBowler(prev => ({ ...prev, wickets: prev.wickets + 1 }));
    setBowlers(prev => prev.map(b => b.name === bowler.name ? { ...b, wickets: b.wickets + 1 } : b));

    if (wickets + 1 < playersPerTeam - 1) {
      setIsBatsmanModalOpen(true);
    } else {
      endOfBall();
    }
  };

  const handleSelectNextBatsman = (newBatsmanName) => {
    const nextBatsman = batsmen.find(b => b.name === newBatsmanName && b.status === 'not out');
    if (nextBatsman) {
        setStriker(nextBatsman);
        addCommentary(`${newBatsmanName} comes to the crease.`);
    }
    setIsBatsmanModalOpen(false);
    endOfBall();
  };

  const endOfBall = () => {
    const newBalls = balls + 1;
    
    setBowler(prev => ({ ...prev, balls: prev.balls + 1 }));
    setBowlers(bowlers.map(b => b.name === bowler.name ? { ...b, balls: b.balls + 1 } : b));

    if (newBalls === 6) {
      const newOvers = overs + 1;
      setOvers(newOvers);
      setBalls(0);
      
      setBowler(prev => ({ ...prev, overs: prev.overs + 1, balls: 0 }));
      setBowlers(bowlers.map(b => b.name === bowler.name ? { ...b, overs: b.overs + 1, balls: 0 } : b));

      addCommentary(`End of Over ${newOvers}.`);
      const currentStriker = striker;
      setStriker(nonStriker);
      setNonStriker(currentStriker);

      if (newOvers < totalOvers) {
        setIsBowlerModalOpen(true);
      }
    } else {
      setBalls(newBalls);
    }
  };

  const handleChangeBowler = (newBowlerName) => {
    const nextBowler = bowlers.find(b => b.name === newBowlerName);
    setBowler(nextBowler);
    setIsBowlerModalOpen(false);
    addCommentary(`${newBowlerName} comes into the attack.`);
  };

  const endOfInning = useCallback(() => {
    if (inning === 1) {
      setFirstInningsSummary({ batsmen, bowlers, battingTeam, bowlingTeam, score, wickets, overs, balls });
      setTarget(score + 1);
      setGameState('inning_break');
    } else {
      setGameState('finished');
      let winner;
      if (score >= target) {
        winner = battingTeam.name;
      } else if (score < target - 1) {
        winner = bowlingTeam.name;
      } else {
        winner = 'Match Tied';
      }
      addCommentary(`Match Finished! ${winner === 'Match Tied' ? 'It\'s a tie!' : `${winner} wins!`}`);
    }
  }, [inning, score, battingTeam, bowlingTeam, target, batsmen, bowlers, overs, balls]);

  const startSecondInning = () => {
    const secondBattingTeam = bowlingTeam;
    const secondBowlingTeam = battingTeam;

    setBattingTeam(secondBattingTeam);
    setBowlingTeam(secondBowlingTeam);

    setScore(0);
    setWickets(0);
    setOvers(0);
    setBalls(0);
    setCommentary([]);

    const initialBatsmen = secondBattingTeam.players.map(p => ({ name: p, runs: 0, balls: 0, fours: 0, sixes: 0, status: 'not out' }));
    const initialBowlers = secondBowlingTeam.players.map(p => ({ name: p, overs: 0, balls: 0, runs: 0, wickets: 0 }));
    
    setBatsmen(initialBatsmen);
    setBowlers(initialBowlers);

    setStriker(initialBatsmen.find(p => p.name === secondInningStriker));
    setNonStriker(initialBatsmen.find(p => p.name === secondInningNonStriker));
    setBowler(initialBowlers.find(p => p.name === secondInningBowler));

    setInning(2);
    setGameState('playing');
    addCommentary(`2nd Innings starts! ${secondBattingTeam.name} needs ${target} to win.`);
  };

  const resetMatch = () => window.location.reload();

  const handlePlayerNameChange = (team, index, newName) => {
      if (team === 1) {
        setTeam1(prev => ({...prev, players: prev.players.map((p, i) => i === index ? newName : p)}));
      } else {
        setTeam2(prev => ({...prev, players: prev.players.map((p, i) => i === index ? newName : p)}));
      }
  };

  const handlePlayersPerTeamChange = (e) => {
    const newSize = parseInt(e.target.value, 10);
    if (isNaN(newSize) || newSize < 2) return;

    setPlayersPerTeam(newSize);

    const adjustPlayerList = (currentPlayers) => {
        const currentSize = currentPlayers.length;
        if (newSize > currentSize) {
            return [...currentPlayers, ...Array(newSize - currentSize).fill('').map((_, i) => `Player ${currentSize + i + 1}`)];
        }
        return currentPlayers.slice(0, newSize);
    };

    setTeam1(prev => ({ ...prev, players: adjustPlayerList(prev.players) }));
    setTeam2(prev => ({ ...prev, players: adjustPlayerList(prev.players) }));
  };

  useEffect(() => {
    if (gameState !== 'playing' || inning === 0) return;

    const conditionsMet = 
      (overs === totalOvers) ||
      (wickets === playersPerTeam - 1) ||
      (inning === 2 && score >= target);

    if (conditionsMet) {
      endOfInning();
    }
  }, [overs, wickets, score, gameState, totalOvers, playersPerTeam, inning, target, endOfInning]);

  // --- Render Functions ---
  const renderSetup = () => {
    const setupBattingTeam = (tossWinner === team1.name && decision === 'bat') || (tossWinner === team2.name && decision === 'bowl') ? team1 : team2;
    const setupBowlingTeam = setupBattingTeam === team1 ? team2 : team1;

    return (
    <div className="w-full max-w-4xl mx-auto p-8 bg-gray-800 bg-opacity-50 backdrop-blur-sm rounded-2xl shadow-2xl border border-gray-700">
      <h1 className="text-4xl font-bold text-center text-white mb-8 tracking-wider">Cricket Match Setup</h1>
      
      <div className="grid grid-cols-1 sm:grid-cols-2 gap-8 mb-8">
          <div>
              <label className="block text-green-400 text-sm font-bold mb-2">Players Per Team</label>
              <input type="number" value={playersPerTeam} onChange={handlePlayersPerTeamChange} className="w-full bg-gray-700 text-white p-3 rounded-lg focus:outline-none focus:ring-2 focus:ring-green-500" />
          </div>
          <div>
              <label className="block text-green-400 text-sm font-bold mb-2">Total Overs</label>
              <input type="number" value={totalOvers} onChange={(e) => setTotalOvers(parseInt(e.target.value))} className="w-full bg-gray-700 text-white p-3 rounded-lg focus:outline-none focus:ring-2 focus:ring-green-500" />
          </div>
          <div>
              <label className="block text-green-400 text-sm font-bold mb-2">Toss Winner</label>
              <select value={tossWinner} onChange={(e) => setTossWinner(e.target.value)} className="w-full bg-gray-700 text-white p-3 rounded-lg focus:outline-none focus:ring-2 focus:ring-green-500">
                  <option value="">Select Team</option>
                  <option value={team1.name}>{team1.name}</option>
                  <option value={team2.name}>{team2.name}</option>
              </select>
          </div>
          <div className="space-y-4">
            <label htmlFor="addRunForWide" className="flex items-center cursor-pointer">
                <div className="relative">
                    <input type="checkbox" id="addRunForWide" className="sr-only peer" checked={addRunForWide} onChange={() => setAddRunForWide(!addRunForWide)} />
                    <div className="block bg-gray-600 w-14 h-8 rounded-full"></div>
                    <div className="dot absolute left-1 top-1 bg-white w-6 h-6 rounded-full transition peer-checked:translate-x-full peer-checked:bg-green-400"></div>
                </div>
                <div className="ml-3 text-white font-medium">Add run for Wide</div>
            </label>
            <label htmlFor="addRunForNoBall" className="flex items-center cursor-pointer">
                <div className="relative">
                    <input type="checkbox" id="addRunForNoBall" className="sr-only peer" checked={addRunForNoBall} onChange={() => setAddRunForNoBall(!addRunForNoBall)} />
                    <div className="block bg-gray-600 w-14 h-8 rounded-full"></div>
                    <div className="dot absolute left-1 top-1 bg-white w-6 h-6 rounded-full transition peer-checked:translate-x-full peer-checked:bg-green-400"></div>
                </div>
                <div className="ml-3 text-white font-medium">Add run for No Ball</div>
            </label>
          </div>
      </div>

      <div className="grid grid-cols-1 md:grid-cols-2 gap-8">
        <div>
          <label className="block text-green-400 text-sm font-bold mb-2">Team 1 Name</label>
          <input type="text" value={team1.name} onChange={(e) => setTeam1({...team1, name: e.target.value})} className="w-full bg-gray-700 text-white p-3 rounded-lg focus:outline-none focus:ring-2 focus:ring-green-500" />
          <div className="mt-4">
              <h3 className="text-lg font-semibold text-white mb-2">Edit {team1.name} Players</h3>
              <div className="grid grid-cols-1 sm:grid-cols-2 gap-2">
                  {team1.players.map((player, index) => <input key={index} type="text" value={player} onChange={(e) => handlePlayerNameChange(1, index, e.target.value)} className="w-full bg-gray-600 text-white p-2 rounded-md text-sm focus:outline-none focus:ring-1 focus:ring-green-400" />)}
              </div>
          </div>
        </div>
        <div>
            <label className="block text-green-400 text-sm font-bold mb-2">Team 2 Name</label>
            <input type="text" value={team2.name} onChange={(e) => setTeam2({...team2, name: e.target.value})} className="w-full bg-gray-700 text-white p-3 rounded-lg focus:outline-none focus:ring-2 focus:ring-green-500" />
            <div className="mt-4">
              <h3 className="text-lg font-semibold text-white mb-2">Edit {team2.name} Players</h3>
              <div className="grid grid-cols-1 sm:grid-cols-2 gap-2">
                  {team2.players.map((player, index) => <input key={index} type="text" value={player} onChange={(e) => handlePlayerNameChange(2, index, e.target.value)} className="w-full bg-gray-600 text-white p-2 rounded-md text-sm focus:outline-none focus:ring-1 focus:ring-green-400" />)}
              </div>
          </div>
        </div>
        
        <div className="md:col-span-2">
          <label className="block text-green-400 text-sm font-bold mb-2">Decision</label>
          <div className="flex gap-4"><button onClick={() => setDecision('bat')} className={`w-full p-3 rounded-lg font-bold transition-all ${decision === 'bat' ? 'bg-green-500 text-gray-900' : 'bg-gray-700 text-white hover:bg-gray-600'}`}>Bat</button><button onClick={() => setDecision('bowl')} className={`w-full p-3 rounded-lg font-bold transition-all ${decision === 'bowl' ? 'bg-green-500 text-gray-900' : 'bg-gray-700 text-white hover:bg-gray-600'}`}>Bowl</button></div>
        </div>

        {tossWinner && decision && (
            <div className="md:col-span-2 mt-4 p-4 bg-gray-700/50 rounded-lg">
                <h3 className="text-xl font-bold text-white mb-4">Select Opening Players</h3>
                <div className="grid grid-cols-1 sm:grid-cols-3 gap-4">
                    <div>
                        <label className="block text-green-400 text-sm font-bold mb-2">Striker ({setupBattingTeam.name})</label>
                        <select value={openingStriker} onChange={e => setOpeningStriker(e.target.value)} className="w-full bg-gray-600 text-white p-2 rounded-md"><option value="">Select Batsman</option>{setupBattingTeam.players.filter(p => p !== openingNonStriker).map(p => <option key={p} value={p}>{p}</option>)}</select>
                    </div>
                    <div>
                        <label className="block text-green-400 text-sm font-bold mb-2">Non-Striker ({setupBattingTeam.name})</label>
                        <select value={openingNonStriker} onChange={e => setOpeningNonStriker(e.target.value)} className="w-full bg-gray-600 text-white p-2 rounded-md"><option value="">Select Batsman</option>{setupBattingTeam.players.filter(p => p !== openingStriker).map(p => <option key={p} value={p}>{p}</option>)}</select>
                    </div>
                    <div>
                        <label className="block text-green-400 text-sm font-bold mb-2">Bowler ({setupBowlingTeam.name})</label>
                        <select value={openingBowler} onChange={e => setOpeningBowler(e.target.value)} className="w-full bg-gray-600 text-white p-2 rounded-md"><option value="">Select Bowler</option>{setupBowlingTeam.players.map(p => <option key={p} value={p}>{p}</option>)}</select>
                    </div>
                </div>
            </div>
        )}
      </div>
      <button onClick={startMatch} disabled={!tossWinner || !decision || !openingStriker || !openingNonStriker || !openingBowler} className="w-full mt-8 bg-green-600 text-white font-bold text-lg p-4 rounded-lg hover:bg-green-700 transition-all disabled:bg-gray-500 disabled:cursor-not-allowed">Start Match</button>
    </div>
    );
  };

  const renderInningBreak = () => {
    const secondInningsBattingTeam = bowlingTeam;
    const secondInningsBowlingTeam = battingTeam;

    return (
        <div className="w-full max-w-2xl mx-auto p-8 bg-gray-800 bg-opacity-70 backdrop-blur-sm rounded-2xl shadow-2xl border border-gray-700 text-center">
            {!showSecondInningSelector ? (
                <>
                    <h2 className="text-3xl font-bold text-white mb-4">Innings Break</h2>
                    <p className="text-lg text-gray-300 mb-2">{battingTeam.name} scored {score}/{wickets} in {overs}.{balls} overs.</p>
                    <p className="text-2xl text-green-400 font-bold mb-6">Target for {bowlingTeam.name} is {target}</p>
                    <button onClick={() => setShowSecondInningSelector(true)} className="bg-blue-600 text-white font-bold text-lg py-3 px-8 rounded-lg hover:bg-blue-700 transition-all">Proceed</button>
                </>
            ) : (
                <>
                    <h2 className="text-3xl font-bold text-white mb-6">Select 2nd Innings Players</h2>
                    <div className="space-y-4 text-left">
                        <div>
                            <label className="block text-green-400 text-sm font-bold mb-2">Striker ({secondInningsBattingTeam.name})</label>
                            <select value={secondInningStriker} onChange={e => setSecondInningStriker(e.target.value)} className="w-full bg-gray-700 text-white p-3 rounded-lg"><option value="">Select Batsman</option>{secondInningsBattingTeam.players.filter(p => p !== secondInningNonStriker).map(p => <option key={p} value={p}>{p}</option>)}</select>
                        </div>
                        <div>
                            <label className="block text-green-400 text-sm font-bold mb-2">Non-Striker ({secondInningsBattingTeam.name})</label>
                            <select value={secondInningNonStriker} onChange={e => setSecondInningNonStriker(e.target.value)} className="w-full bg-gray-700 text-white p-3 rounded-lg"><option value="">Select Batsman</option>{secondInningsBattingTeam.players.filter(p => p !== secondInningStriker).map(p => <option key={p} value={p}>{p}</option>)}</select>
                        </div>
                        <div>
                            <label className="block text-green-400 text-sm font-bold mb-2">Bowler ({secondInningsBowlingTeam.name})</label>
                            <select value={secondInningBowler} onChange={e => setSecondInningBowler(e.target.value)} className="w-full bg-gray-700 text-white p-3 rounded-lg"><option value="">Select Bowler</option>{secondInningsBowlingTeam.players.map(p => <option key={p} value={p}>{p}</option>)}</select>
                        </div>
                    </div>
                    <button onClick={startSecondInning} disabled={!secondInningStriker || !secondInningNonStriker || !secondInningBowler} className="w-full mt-8 bg-green-600 text-white font-bold text-lg p-4 rounded-lg hover:bg-green-700 transition-all disabled:bg-gray-500">Start 2nd Innings</button>
                </>
            )}
        </div>
    );
  };

  const renderFinished = () => (
    <div className="w-full max-w-4xl mx-auto p-8 bg-gray-800 bg-opacity-70 backdrop-blur-sm rounded-2xl shadow-2xl border border-gray-700">
        <h2 className="text-4xl font-bold text-center text-green-400 mb-4">Match Finished!</h2>
        <p className="text-xl text-center text-white mb-6">{commentary[0]?.split(': ')[1]}</p>
        
        <div className="space-y-6">
            {/* First Innings Scorecard */}
            <div className="bg-gray-900/50 p-4 rounded-lg">
                <h3 className="text-xl font-bold text-white mb-2">{firstInningsSummary.battingTeam.name} Innings ({firstInningsSummary.score}/{firstInningsSummary.wickets} in {formatOvers(firstInningsSummary.overs, firstInningsSummary.balls)} overs)</h3>
                <table className="w-full text-sm text-left text-gray-300">
                    <thead className="text-xs text-green-400 uppercase"><tr><th className="py-2 px-2">Batsman</th><th className="py-2 px-2">R</th><th className="py-2 px-2">B</th><th className="py-2 px-2">4s</th><th className="py-2 px-2">6s</th><th className="py-2 px-2">SR</th></tr></thead>
                    <tbody>{firstInningsSummary.batsmen.map(b => ( <tr key={b.name} className="border-b border-gray-700"><td className="py-2 px-2 font-medium text-white">{b.name} <span className="text-gray-400 text-xs">{b.status}</span></td><td className="py-2 px-2">{b.runs}</td><td className="py-2 px-2">{b.balls}</td><td className="py-2 px-2">{b.fours}</td><td className="py-2 px-2">{b.sixes}</td><td className="py-2 px-2">{calculateStrikeRate(b.runs, b.balls)}</td></tr>))}</tbody>
                </table>
                <table className="w-full text-sm text-left text-gray-300 mt-4">
                    <thead className="text-xs text-red-400 uppercase"><tr><th className="py-2 px-2">Bowler</th><th className="py-2 px-2">O</th><th className="py-2 px-2">R</th><th className="py-2 px-2">W</th><th className="py-2 px-2">Econ</th></tr></thead>
                    <tbody>{firstInningsSummary.bowlers.filter(b => b.balls > 0).map(b => ( <tr key={b.name} className="border-b border-gray-700"><td className="py-2 px-2 font-medium text-white">{b.name}</td><td className="py-2 px-2">{formatOvers(Math.floor(b.balls / 6), b.balls % 6)}</td><td className="py-2 px-2">{b.runs}</td><td className="py-2 px-2">{b.wickets}</td><td className="py-2 px-2">{calculateEconomy(b.runs, b.balls)}</td></tr>))}</tbody>
                </table>
            </div>

            {/* Second Innings Scorecard */}
            <div className="bg-gray-900/50 p-4 rounded-lg">
                <h3 className="text-xl font-bold text-white mb-2">{battingTeam.name} Innings ({score}/{wickets} in {formatOvers(overs, balls)} overs)</h3>
                <table className="w-full text-sm text-left text-gray-300">
                    <thead className="text-xs text-green-400 uppercase"><tr><th className="py-2 px-2">Batsman</th><th className="py-2 px-2">R</th><th className="py-2 px-2">B</th><th className="py-2 px-2">4s</th><th className="py-2 px-2">6s</th><th className="py-2 px-2">SR</th></tr></thead>
                    <tbody>{batsmen.map(b => ( <tr key={b.name} className="border-b border-gray-700"><td className="py-2 px-2 font-medium text-white">{b.name} <span className="text-gray-400 text-xs">{b.status}</span></td><td className="py-2 px-2">{b.runs}</td><td className="py-2 px-2">{b.balls}</td><td className="py-2 px-2">{b.fours}</td><td className="py-2 px-2">{b.sixes}</td><td className="py-2 px-2">{calculateStrikeRate(b.runs, b.balls)}</td></tr>))}</tbody>
                </table>
                <table className="w-full text-sm text-left text-gray-300 mt-4">
                    <thead className="text-xs text-red-400 uppercase"><tr><th className="py-2 px-2">Bowler</th><th className="py-2 px-2">O</th><th className="py-2 px-2">R</th><th className="py-2 px-2">W</th><th className="py-2 px-2">Econ</th></tr></thead>
                    <tbody>{bowlers.filter(b => b.balls > 0).map(b => ( <tr key={b.name} className="border-b border-gray-700"><td className="py-2 px-2 font-medium text-white">{b.name}</td><td className="py-2 px-2">{formatOvers(Math.floor(b.balls / 6), b.balls % 6)}</td><td className="py-2 px-2">{b.runs}</td><td className="py-2 px-2">{b.wickets}</td><td className="py-2 px-2">{calculateEconomy(b.runs, b.balls)}</td></tr>))}</tbody>
                </table>
            </div>
        </div>
        <button onClick={resetMatch} className="w-full mt-8 bg-blue-600 text-white font-bold text-lg p-4 rounded-lg hover:bg-blue-700 transition-all">New Match</button>
    </div>
  );
  
  const BowlerSelectionModal = ({ isOpen, onSelect }) => {
    const [selectedBowler, setSelectedBowler] = useState('');
    if (!isOpen) return null;
    const availableBowlers = bowlingTeam.players.filter(p => p !== bowler.name);
    return (
        <div className="fixed inset-0 bg-black bg-opacity-70 flex items-center justify-center z-50">
            <div className="bg-gray-800 rounded-2xl p-8 shadow-2xl border border-gray-700 w-full max-w-md">
                <h2 className="text-2xl font-bold text-white mb-6">Select Next Bowler</h2>
                <div className="space-y-3">
                    {availableBowlers.map(p => (
                        <label key={p} className="flex items-center p-3 bg-gray-700 rounded-lg cursor-pointer hover:bg-gray-600 transition">
                            <input type="radio" name="bowler" value={p} checked={selectedBowler === p} onChange={() => setSelectedBowler(p)} className="form-radio h-5 w-5 text-green-500 bg-gray-900 border-gray-600 focus:ring-green-500" />
                            <span className="ml-4 text-lg text-white">{p}</span>
                        </label>
                    ))}
                </div>
                <button onClick={() => onSelect(selectedBowler)} disabled={!selectedBowler} className="w-full mt-6 bg-green-600 text-white font-bold text-lg p-3 rounded-lg hover:bg-green-700 transition disabled:bg-gray-500">Confirm</button>
            </div>
        </div>
    );
  };

  const BatsmanSelectionModal = ({ isOpen, onSelect }) => {
    const [selectedBatsman, setSelectedBatsman] = useState('');
    if (!isOpen) return null;
    const availableBatsmen = batsmen.filter(b => b.status === 'not out' && b.name !== nonStriker?.name);
    return (
        <div className="fixed inset-0 bg-black bg-opacity-70 flex items-center justify-center z-50">
            <div className="bg-gray-800 rounded-2xl p-8 shadow-2xl border border-gray-700 w-full max-w-md">
                <h2 className="text-2xl font-bold text-white mb-6">Select Next Batsman</h2>
                <div className="space-y-3 max-h-60 overflow-y-auto pr-2">
                    {availableBatsmen.map(p => (
                        <label key={p.name} className="flex items-center p-3 bg-gray-700 rounded-lg cursor-pointer hover:bg-gray-600 transition">
                            <input type="radio" name="batsman" value={p.name} checked={selectedBatsman === p.name} onChange={() => setSelectedBatsman(p.name)} className="form-radio h-5 w-5 text-green-500 bg-gray-900 border-gray-600 focus:ring-green-500" />
                            <span className="ml-4 text-lg text-white">{p.name}</span>
                        </label>
                    ))}
                </div>
                <button onClick={() => onSelect(selectedBatsman)} disabled={!selectedBatsman} className="w-full mt-6 bg-green-600 text-white font-bold text-lg p-3 rounded-lg hover:bg-green-700 transition disabled:bg-gray-500">Confirm</button>
            </div>
        </div>
    );
  };

  const NoBallRunsModal = ({ isOpen, onSelect }) => {
    if (!isOpen) return null;
    return (
        <div className="fixed inset-0 bg-black bg-opacity-70 flex items-center justify-center z-50">
            <div className="bg-gray-800 rounded-2xl p-8 shadow-2xl border border-gray-700 w-full max-w-md">
                <h2 className="text-2xl font-bold text-white mb-6">Runs Scored off No Ball?</h2>
                <div className="grid grid-cols-4 gap-3">
                    {[0, 1, 2, 3, 4, 6].map(run => (
                        <button key={run} onClick={() => onSelect(run)} className={`control-btn ${run === 4 ? 'bg-blue-600' : run === 6 ? 'bg-purple-600' : 'bg-gray-600'}`}>
                            {run}
                        </button>
                    ))}
                </div>
            </div>
        </div>
    );
  };

  const renderPlaying = () => (
    <div className="grid grid-cols-1 lg:grid-cols-3 gap-6 p-4 md:p-6 max-w-7xl mx-auto">
      <div className="lg:col-span-2 space-y-6">
        <div className="bg-gray-800 bg-opacity-50 backdrop-blur-md rounded-2xl shadow-lg p-6 border border-gray-700">
          <div className="flex justify-between items-center"><h2 className="text-3xl font-bold text-white tracking-wider">{battingTeam.name}</h2>{inning === 2 && <div className="text-lg text-green-400">Target: <span className="font-bold">{target}</span></div>}</div>
          <div className="my-4 text-center"><span className="text-7xl font-bold text-white">{score}</span><span className="text-5xl font-semibold text-gray-300">/</span><span className="text-7xl font-bold text-red-500">{wickets}</span></div>
          <div className="flex justify-between items-center text-xl text-gray-200"><div>Overs: <span className="font-bold text-white">{formatOvers(overs, balls)} / {totalOvers}</span></div><div>Run Rate: <span className="font-bold text-white">{((score / (overs * 6 + balls)) * 6 || 0).toFixed(2)}</span></div></div>
        </div>
        <div className="grid md:grid-cols-2 gap-6">
            <div className="bg-gray-800 bg-opacity-50 backdrop-blur-md rounded-2xl shadow-lg p-4 border border-gray-700"><h3 className="text-lg font-semibold text-green-400 mb-2 flex items-center"><BatIcon /> Batsmen</h3>
                <div className="space-y-2 text-white">
                    <div className="flex justify-between"><span>{striker?.name}*</span> <span className="text-gray-300">{striker?.runs} ({striker?.balls}) <span className="text-xs text-gray-400">SR: {calculateStrikeRate(striker?.runs, striker?.balls)}</span></span></div>
                    <div className="flex justify-between"><span>{nonStriker?.name}</span> <span className="text-gray-300">{nonStriker?.runs} ({nonStriker?.balls}) <span className="text-xs text-gray-400">SR: {calculateStrikeRate(nonStriker?.runs, nonStriker?.balls)}</span></span></div>
                </div>
            </div>
            <div className="bg-gray-800 bg-opacity-50 backdrop-blur-md rounded-2xl shadow-lg p-4 border border-gray-700"><h3 className="text-lg font-semibold text-red-400 mb-2 flex items-center"><BallIcon /> Bowler</h3>
                <div className="space-y-2 text-white">
                    <div className="flex justify-between"><span>{bowler?.name}*</span> <span className="text-gray-300">{bowler?.wickets}/{bowler?.runs} ({formatOvers(Math.floor(bowler?.balls/6), bowler?.balls % 6)}) <span className="text-xs text-gray-400">Econ: {calculateEconomy(bowler?.runs, bowler?.balls)}</span></span></div>
                </div>
            </div>
        </div>
        <div className="bg-gray-800 bg-opacity-50 backdrop-blur-md rounded-2xl shadow-lg p-6 border border-gray-700">
          <div className="grid grid-cols-4 gap-3"><button onClick={() => handleRun(0)} className="control-btn bg-gray-600">0</button><button onClick={() => handleRun(1)} className="control-btn bg-gray-600">1</button><button onClick={() => handleRun(2)} className="control-btn bg-gray-600">2</button><button onClick={() => handleRun(3)} className="control-btn bg-gray-600">3</button><button onClick={() => handleRun(4)} className="control-btn bg-blue-600">4</button><button onClick={() => handleRun(6)} className="control-btn bg-purple-600">6</button><button onClick={handleWicket} className="control-btn bg-red-600 col-span-2">Wicket</button></div>
          <div className="grid grid-cols-4 gap-3 mt-3"><button onClick={() => handleExtra('Wide')} className="control-btn text-sm bg-yellow-600">Wide</button><button onClick={() => handleExtra('No Ball')} className="control-btn text-sm bg-yellow-600">No Ball</button><button onClick={() => handleExtra('Bye')} className="control-btn text-sm bg-yellow-600">Bye</button><button onClick={() => handleExtra('Leg Bye')} className="control-btn text-sm bg-yellow-600">Leg Bye</button></div>
        </div>
      </div>
      <div className="space-y-6">
        <div className="bg-gray-800 bg-opacity-50 backdrop-blur-md rounded-2xl shadow-lg p-4 border border-gray-700 h-64 overflow-y-auto flex flex-col-reverse"><div className="space-y-2 text-sm">{commentary.map((c, i) => <p key={i} className="text-gray-300">{c}</p>)}</div><h3 className="text-lg font-semibold text-green-400 mb-2 sticky top-0 bg-gray-800 bg-opacity-50 py-2">Commentary</h3></div>
        <div className="bg-gray-800 bg-opacity-50 backdrop-blur-md rounded-2xl shadow-lg p-4 border border-gray-700">
          <h3 className="text-lg font-semibold text-green-400 mb-2">Full Scorecard</h3>
          <div className="overflow-x-auto">
            <h4 className="text-md font-semibold text-white mb-1">{battingTeam?.name}</h4>
            <table className="w-full text-sm text-left text-gray-300">
              <thead className="text-xs text-green-400 uppercase"><tr><th className="py-2 px-2">Batsman</th><th className="py-2 px-2">R</th><th className="py-2 px-2">B</th><th className="py-2 px-2">4s</th><th className="py-2 px-2">6s</th><th className="py-2 px-2">SR</th></tr></thead>
              <tbody>{batsmen.filter(b => b.balls > 0 || b.status === 'out').map(b => ( <tr key={b.name} className="border-b border-gray-700"><td className="py-2 px-2 font-medium text-white">{b.name} <span className="text-gray-400 text-xs">{b.status}</span></td><td className="py-2 px-2">{b.runs}</td><td className="py-2 px-2">{b.balls}</td><td className="py-2 px-2">{b.fours}</td><td className="py-2 px-2">{b.sixes}</td><td className="py-2 px-2">{calculateStrikeRate(b.runs, b.balls)}</td></tr>))}</tbody>
            </table>
            <h4 className="text-md font-semibold text-white mt-4 mb-1">{bowlingTeam?.name}</h4>
            <table className="w-full text-sm text-left text-gray-300">
              <thead className="text-xs text-red-400 uppercase"><tr><th className="py-2 px-2">Bowler</th><th className="py-2 px-2">O</th><th className="py-2 px-2">R</th><th className="py-2 px-2">W</th><th className="py-2 px-2">Econ</th></tr></thead>
              <tbody>{bowlers.filter(b => b.balls > 0).map(b => ( <tr key={b.name} className="border-b border-gray-700"><td className="py-2 px-2 font-medium text-white">{b.name}</td><td className="py-2 px-2">{formatOvers(Math.floor(b.balls / 6), b.balls % 6)}</td><td className="py-2 px-2">{b.runs}</td><td className="py-2 px-2">{b.wickets}</td><td className="py-2 px-2">{calculateEconomy(b.runs, b.balls)}</td></tr>))}</tbody>
            </table>
          </div>
        </div>
      </div>
    </div>
  );

  return (
    <main className="min-h-screen bg-gray-900 text-white font-sans relative overflow-hidden">
      <div className="absolute inset-0 bg-cover bg-center opacity-10" style={{backgroundImage: "url('https://www.transparenttextures.com/patterns/crissxcross.png')"}}></div>
      <div className="absolute top-0 left-0 w-1/2 h-full bg-gradient-to-br from-green-900 to-transparent opacity-20"></div>
      <div className="absolute bottom-0 right-0 w-1/2 h-full bg-gradient-to-tl from-blue-900 to-transparent opacity-20"></div>

      {event && ( <div className="absolute inset-0 flex items-center justify-center pointer-events-none z-50"><div className={`event-graphic text-9xl font-black tracking-widest ${event === '4' && 'text-blue-400'} ${event === '6' && 'text-purple-400'} ${event === 'W' && 'text-red-500'}`}>{event === 'W' ? 'WICKET' : event}</div></div>)}
      
      <BowlerSelectionModal isOpen={isBowlerModalOpen} onSelect={handleChangeBowler} />
      <BatsmanSelectionModal isOpen={isBatsmanModalOpen} onSelect={handleSelectNextBatsman} />
      <NoBallRunsModal isOpen={isNoBallModalOpen} onSelect={handleNoBallRuns} />

      <div className="relative z-10 flex items-center justify-center min-h-screen">
        {gameState === 'setup' && renderSetup()}
        {gameState === 'playing' && renderPlaying()}
        {gameState === 'inning_break' && renderInningBreak()}
        {gameState === 'finished' && renderFinished()}
      </div>

      <style>{` body { font-family: 'Inter', sans-serif; } .control-btn { padding: 1rem; font-size: 1.25rem; font-weight: bold; border-radius: 0.75rem; color: white; transition: all 0.2s ease-in-out; box-shadow: 0 4px 6px rgba(0,0,0,0.2); } .control-btn:hover { transform: translateY(-2px); box-shadow: 0 8px 12px rgba(0,0,0,0.3); } .control-btn:active { transform: translateY(0); box-shadow: 0 4px 6px rgba(0,0,0,0.2); } @keyframes event-animation { 0% { transform: scale(0.5); opacity: 0; } 50% { transform: scale(1.2); opacity: 1; } 100% { transform: scale(1.5); opacity: 0; } } .event-graphic { animation: event-animation 2s ease-out forwards; text-shadow: 0 0 20px rgba(255, 255, 255, 0.5); }`}</style>
    </main>
  );
}
