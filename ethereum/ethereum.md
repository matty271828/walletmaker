# Creating and working with an Ethereum paper wallet on a Raspberry Pi

This guide outlines how to securely create an Ethereum paper wallet on a Raspberry Pi without connecting it to the internet. The process includes installing Geth, creating a new Ethereum account, and securely backing up your private key.

---

## Prerequisites

- A Raspberry Pi running a Linux-based operating system.
- A USB flash drive to transfer files.
- A secure, minimal environment (avoid internet connection during key generation).

# Step 1: Set Up Go

1. **Navigate to the Directory**:

    ```
    cd /path/to/ethereum/go
    ```

2. **Update the Environment Variables**:

    Open the ~/.bashrc file using nano:

    ```
    nano ~/.bashrc
    ```

    Scroll to the bottom of the file and add the following line. Save and exit the file.

    ```
    export PATH=$PATH:/path/to/ethereum/go/bin
    export GOROOT=/path/to/ethereum/go
    export GOPATH=$HOME/go
    ```

    Apply the changes by sourcing the .bashrc file:

    ```
    source ~/.bashrc
    ```

3. **Ensure Go has executable permissions**:

   ```
   chmod +x /path/to/ethereum/go/bin/go
   ```

4. **Verify the Installation**:

   ```
   go --version
   ```

# Step 2: Install Geth

1. **Navigate to the Directory**:

   ```
   cd /path/to/ethereum/geth
   ```

3. **Ensure Geth has executable permissions**:

   ```
    chmod +x /path/to/ethereum/geth
   ```

4. **Verify the Installation**:

   ```
   geth --version
   ```

---

# Step 3: Create a New Ethereum Account

1. **Create a New Ethereum Account**:

   ```
   geth account new
   ```

Enter a password for the account when prompted. The output will show the path to the 
keystore file, which securely stores your account's private key.

Example output:
    ```
    Your new account is locked with a password. Please give a password. 
    Passphrase: 
    Repeat passphrase: 
    Address: {0xYourAccountAddressHere}
    ```

# Step 4: Deposit Funds to the Account

To deposit funds to the account, purchase ETH from an exchange such as Coinbase or Binance and transfer it to the account address.

# TODO: Step 5: Send funds from the account to another account. 
