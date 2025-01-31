# Blockchain-for-Transparent-and-Secure-Voting-Systems-in-Elections
import React, { useState, useEffect } from "react";

const initialCandidates = [
    { name: "Alice", voteCount: 0 },
    { name: "Bob", voteCount: 0 },
    { name: "Charlie", voteCount: 0 }
];

const LoginPage = ({ onLogin }) => {
    const [phone, setPhone] = useState("");
    const [otp, setOtp] = useState("");
    const [isOtpSent, setIsOtpSent] = useState(false);

    const sendOtp = () => {
        if (phone.length === 10) {
            setIsOtpSent(true);
            alert("OTP sent to " + phone);
        } else {
            alert("Enter a valid phone number");
        }
    };

    const verifyOtp = () => {
        if (otp === "1234") {
            onLogin();
        } else {
            alert("Invalid OTP");
        }
    };

    return (
        <div style={{ textAlign: "center", marginTop: "50px" }}>
            <h2>Login</h2>
            {!isOtpSent ? (
                <div>
                    <input type="text" placeholder="Enter phone number" value={phone} onChange={(e) => setPhone(e.target.value)} />
                    <button onClick={sendOtp}>Send OTP</button>
                </div>
            ) : (
                <div>
                    <input type="text" placeholder="Enter OTP" value={otp} onChange={(e) => setOtp(e.target.value)} />
                    <button onClick={verifyOtp}>Verify OTP</button>
                </div>
            )}
        </div>
    );
};

const VotingApp = () => {
    const [candidates, setCandidates] = useState(() => {
        const savedCandidates = localStorage.getItem("candidates");
        return savedCandidates ? JSON.parse(savedCandidates) : initialCandidates;
    });
    const [hasVoted, setHasVoted] = useState(() => localStorage.getItem("hasVoted") === "true");
    const [isLoggedIn, setIsLoggedIn] = useState(false);

    useEffect(() => {
        localStorage.setItem("candidates", JSON.stringify(candidates));
    }, [candidates]);

    useEffect(() => {
        localStorage.setItem("hasVoted", hasVoted);
    }, [hasVoted]);

    const vote = (index) => {
        if (!hasVoted) {
            setCandidates(prevCandidates => prevCandidates.map((candidate, i) =>
                i === index ? { ...candidate, voteCount: candidate.voteCount + 1 } : candidate
            ));
            setHasVoted(true);
        } else {
            alert("You have already voted!");
        }
    };

    if (!isLoggedIn) {
        return <LoginPage onLogin={() => setIsLoggedIn(true)} />;
    }

    return (
        <div style={{ textAlign: "center", fontFamily: "Arial, sans-serif", marginTop: "20px" }}>
            <h1>Voting System</h1>
            {hasVoted ? <p style={{ color: "green" }}>Thank you for voting!</p> : <p>Please cast your vote.</p>}
            <ul style={{ listStyleType: "none", padding: 0 }}>
                {candidates.map((candidate, index) => (
                    <li key={index} style={{ margin: "10px 0" }}>
                        <strong>{candidate.name}</strong> - <span aria-live="polite">{candidate.voteCount} votes</span>
                        <button 
                            onClick={() => vote(index)}
                            disabled={hasVoted}
                            style={{ marginLeft: "10px", padding: "5px 10px", cursor: hasVoted ? "not-allowed" : "pointer" }}
                        >
                            Vote
                        </button>
                    </li>
                ))}
            </ul>
        </div>
    );
};

export default VotingApp;

