# Project-Function-Frontend
# Project Title

For this project, create a simple contract with 2-3 functions. Then show the values of those functions in frontend of the application. 

## Description

For this project, you'll create a simple Ethereum smart contract with 2-3 functions. These functions could perform various actions, such as storing and retrieving data, performing calculations, or interacting with other contracts.

Firstly, connect your wallet by clicking on connect wallet button.
Your balance will be updated automatically after each transaction.
You can deposit the balance into your account by clicking the deposit button.
You can withdraw funds using Withdraw button.

## Getting Started

### Installing

we will use the starter template and clone it in vs code.
After cloning the github, you will want to do the following to get the code running on your computer.

Inside the project directory, in the terminal type: npm i
Open two additional terminals in your VS code
In the second terminal type: npx hardhat node
In the third terminal, type: npx hardhat run --network localhost scripts/deploy.js
Back in the first terminal, type npm run dev to launch the front-end.
After this, the project will be running on your localhost. Typically at http://localhost:3000/

### Executing program

import { useState, useEffect } from "react";
import { ethers } from "ethers";
import atm_abi from "../artifacts/contracts/Assessment.sol/Assessment.json";

export default function HomePage() {
  const [ethWallet, setEthWallet] = useState(undefined);
  const [account, setAccount] = useState(undefined);
  const [atm, setATM] = useState(undefined);
  const [balance, setBalance] = useState(undefined);
  const [inputAmount, setInputAmount] = useState(0);
  const [error, setError] = useState("");

  const contractAddress = "0x5FbDB2315678afecb367f032d93F642f64180aa3"; // Replace with your contract address
  const atmABI = atm_abi.abi;

  const getWallet = async () => {
    if (window.ethereum) {
      setEthWallet(window.ethereum);
    }

    if (ethWallet) {
      const accounts = await ethWallet.request({ method: "eth_requestAccounts" });
      handleAccount(accounts[0]);
    }
  };

  const handleAccount = (account) => {
    if (account) {
      console.log("Account connected: ", account);
      setAccount(account);
    } else {
      console.log("No account found");
    }
  };

  const connectAccount = async () => {
    if (!ethWallet) {
      setError("MetaMask wallet is required to connect");
      return;
    }

    try {
      const accounts = await ethWallet.request({ method: 'eth_requestAccounts' });
      handleAccount(accounts[0]);
      getATMContract();
      setError("");
    } catch (error) {
      setError("Failed to connect account");
    }
  };

  const getATMContract = () => {
    const provider = new ethers.providers.Web3Provider(ethWallet);
    const signer = provider.getSigner();
    const atmContract = new ethers.Contract(contractAddress, atmABI, signer);
 
    setATM(atmContract);
  };

  const getBalance = async () => {
    if (atm) {
      const balanceInWei = await atm.getBalance();
      setBalance(ethers.utils.formatEther(balanceInWei));
    }
  };

  const deposit = async () => {
    if (atm && inputAmount > 0) {
      try {
        const tx = await atm.deposit({ value: ethers.utils.parseEther(inputAmount.toString()) });
        await tx.wait();
        getBalance();
        setError("");
      } catch (error) {
        setError("Failed to deposit");
      }
    } else {
      setError("Please enter a valid amount");
    }
  };

  const withdraw = async () => {
    if (atm && inputAmount > 0) {
      try {
        const tx = await atm.withdraw(ethers.utils.parseEther(inputAmount.toString()));
        await tx.wait();
        getBalance();
        setError("");
      } catch (error) {
        setError("Failed to withdraw");
      }
    } else {
      setError("Please enter a valid amount");
    }
  };

  useEffect(() => { getWallet(); }, []);

  return (
    <main className="container">
      <header>
        <h1>Welcome to the Metacrafters ATM!</h1>
      </header>
      <div className="content">
        {account ? (
          <div>
            <p>Your Account: {account}</p>
            <p>Your Balance: {balance} ETH</p>
            <input
              type="number"
              value={inputAmount}
              onChange={(e) => setInputAmount(e.target.value)}
              placeholder="Enter amount in ETH"
            />
            <button onClick={deposit}>Deposit</button>
            <button onClick={withdraw}>Withdraw</button>
            {error && <p className="error">{error}</p>}
          </div>
        ) : (
          <button onClick={connectAccount}>Connect your MetaMask wallet</button>
        )}
      </div>
      <style jsx>{`
        .container {
          text-align: center;
          padding: 50px;
          font-family: Arial, sans-serif;
        }
        .content {
          margin-top: 20px;
        }
        input {
          margin-right: 10px;
          padding: 8px;
          border: 1px solid #ccc;
          border-radius: 5px;
        }
        button {
          margin-top: 10px;
          padding: 5px 10px;
          background-color: #007bff;
          color: #fff;
          border: none;
          cursor: pointer;
          transition: background-color 0.3s ease;
        }
        .error {
          color: red;
          margin-top: 10px;
        }
      `}</style>
    </main>
  );
}

