import { useState, useEffect } from "react";
import { ethers } from "ethers";
import atm_abi from "../artifacts/contracts/Assessment.sol/Assessment.json";

export default function HomePage() {
  const [ethWallet, setEthWallet] = useState(undefined);
  const [account, setAccount] = useState(undefined);
  const [atm, setATM] = useState(undefined);
  const [balance, setBalance] = useState(undefined);

  const contractAddress = "0x5FbDB2315678afecb367f032d93F642f64180aa3";
  const atmABI = atm_abi.abi;

  const getWallet = async () => {
    if (window.ethereum) {
      setEthWallet(window.ethereum);
    }

    if (ethWallet) {
      const account = await ethWallet.request({ method: "eth_accounts" });
      handleAccount(account);
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
      alert("MetaMask wallet is required to connect");
      return;
    }

    const accounts = await ethWallet.request({ method: "eth_requestAccounts" });
    handleAccount(accounts);

    // once wallet is set we can get a reference to our deployed contract
    getATMContract();
  };

  const getATMContract = () => {
    const provider = new ethers.providers.Web3Provider(ethWallet);
    const signer = provider.getSigner();
    const atmContract = new ethers.Contract(contractAddress, atmABI, signer);

    setATM(atmContract);
  };

  const getBalance = async () => {
    if (atm) {
      setBalance((await atm.getBalance()).toNumber());
    }
  };

  const deposit = async () => {
    if (atm) {
      let tx = await atm.deposit(1);
      await tx.wait();
      getBalance();
    }
  };

  const withdraw = async () => {
    if (atm) {
      let tx = await atm.withdraw(1);
      await tx.wait();
      getBalance();
    }
  };

  const initUser = () => {
    // Check to see if user has Metamask
    if (!ethWallet) {
      return <p>Please install MetaMask to use this ATM.</p>;
    }

    // Check to see if user is connected. If not, connect to their account
    if (!account) {
      return (
        <input
          type="button"
          value="Connect MetaMask Wallet"
          onClick={connectAccount}
          className="btn"
        />
      );
    }

    if (balance === undefined) {
      getBalance();
    }

    return (
      <div className="user-info">
        <p>Account: {account}</p>
        <p>Balance: {balance} ETH</p>
        <div className="button-container">
          <input
            type="button"
            value="Deposit 1 ETH"
            onClick={deposit}
            className="btn deposit-btn"
          />
          <input
            type="button"
            value="Withdraw 1 ETH"
            onClick={withdraw}
            className="btn withdraw-btn"
          />
        </div>
      </div>
    );
  };

  useEffect(() => {
    getWallet();
  }, []);

  return (
    <main className="container">
      <header>
        <h1>Metacrafters ATM</h1>
      </header>
      {initUser()}
      <style jsx>{`
        .container {
          display: flex;
          flex-direction: column;
          align-items: center;
          justify-content: center;
          min-height: 100vh;
          background-color: #f4f4f9;
          color: #333;
          font-family: Arial, sans-serif;
          padding: 20px;
        }
        header {
          margin-bottom: 20px;
          text-align: center;
        }
        h1 {
          font-size: 2.5em;
          color: #0070f3;
        }
        .user-info {
          background: #fff;
          padding: 20px;
          border-radius: 8px;
          box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
          max-width: 300px;
          text-align: center;
          margin: 20px 0;
        }
        p {
          font-size: 1.1em;
          margin: 10px 0;
        }
        .btn {
          padding: 10px 15px;
          font-size: 1em;
          border-radius: 20px;
          cursor: pointer;
          margin: 10px 0;
          transition: background-color 0.3s ease, transform 0.2s ease;
          display: inline-block;
          border: none;
        }
        .btn:hover {
          transform: scale(1.05);
        }
        .deposit-btn {
          background-color: #4caf50;
          color: white;
        }
        .deposit-btn:hover {
          background-color: #45a049;
        }
        .withdraw-btn {
          background-color: #f44336;
          color: white;
        }
        .withdraw-btn:hover {
          background-color: #e53935;
        }
        .button-container {
          max-height: 100px;
          overflow-y: auto;
          padding: 5px;
        }
      `}</style>
    </main>
  );
}
