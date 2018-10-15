# Documentation

Table of contents
=================

<!--ts-->
   * [Small count UTXO](#small-count-utxo)
   * [Balance is smaller](#balance-is-smaller)
   * [Last notary BTC was more than 3 hours ago](#last-notary-btc-was-more-than-3-hours-ago)
   * [Bitcoind process is not running](#bitcoind-process-is-not-running)
   * [BTC "is_mine" is not true](#btc-is_mine-is-not-true)
   * [Iguana process is not running](#iguana-process-is-not-running)
   * [Wallet size is too big, time to clean it up!](#wallet-size-is-too-big-time-to-clean-it-up)
      * [Fresh BTC wallet](#fresh-btc-wallet)
      * [Fresh KMD wallet](#fresh-kmd-wallet)
         * [Auto freshing](#auto-freshing)
         * [Manual freshing](#auto-freshing)
      * [Fresh other wallet](#fresh-other-wallet)
   * [Update Komodo](#update-komodo)
   * [Update Iguana](#update-iguana)
  
<!--te-->

### Small count UTXO
**Warning: All command run into user komodo**
We have scripts for auto slit UTXO. But if when it does not work, we must split in manual, and fix for future auto split.
To split UTXO use a command:

```
$ ./acsplit {coin_name} 50
```
if you receive error :```{"error":"couldnt create duplicates tx","tag":"xxxxxxxxx"}```, send 0.1 funds from your wallet to same address, wait confirmations
(you can use command  to check confirmation ```komodo-cli -ac_name={coin_name} listtransactions```), after confirmation run the command for splitting again

### Balance is smaller
**Warning: All command run into user komodo**
You get this message if you balace in fund is < 0.2
When your balance comes to the critical it’s time to think about topping it up
You can check balance for this coin in other node, use command:  
for BTC: ``` bitcoin-cli getbalance ```  
for KMD: ``` komodo-cli getbalance ```  
for other coins: ``` komodo-cli -ac_name=COIN_NAME getbalance ```  
Check fund in other node, if you have enough funds to transfer to another node, transfer funds using the following commands:
We can look address for transfer, on the node to which you want to transfer funds, use commands:  
for BTC: ``` echo ${BTC_ADDRESS} ```  
for KMD and other coins: ``` echo ${KMD_ADDRESS} ```  
To transfer fund, run command in node, from which you want to transfer funds:  
for BTC: ``` bitcoin-cli sendtoaddress BTC_ADDRESS_OTHER_NODE COUNT_FUNDS ```  
for KMD: ``` komodo-cli sendtoaddress KMD_ADDRESS_OTHER_NODE COUNT_FUNDS ```  
for other coins: ``` komodo-cli -ac_name=COIN_NAME sendtoaddress KMD_ADDRESS_OTHER_NODE COUNT_FUNDS ```  

If you balance is other node small too, you can ask for funds transfer  Kolo in discord chat.

### balance is very small

You get this message if you balace in fund is < 0.05  
Your balance is very small, If you do not fill it urgently, you will lose notarisations for this fund.
You must urgently start instructions in [Balance is smaller](#balance-is-smaller)

### Last notary BTC was more than 3 hours ago
If your node wasn’t notarised more than 3 hours ago there can be some problems
1. Check process iguana, use command: ``` ps aux | grep iguana ```, you must see ```../agents/iguana```, if process crached, run iguana again, use script ```~/notary```
2. Check update for komodo and iguana
  2a. update komodo:
      ```
      cd ~/komodo
      git pull
      ~/komodotools/webworker01/rebuildkomodo
      ```
      for restart komodo only for KMD
      ```
      komodo-cli stop
      source ~/komodo/src/pubkey.txt
      ./src/komodod  -gen -genproclimit=2 -notary -pubkey="${pubkey}" &
      ```
      for restart komodo for all coins:
      ```
      komodo-cli stop
      cd komodo/src/
      ./fiat-cli stop
      source ~/komodo/src/pubkey.txt
      komodod  -gen -genproclimit=2 -notary -pubkey="${pubkey}" &
      ./assetchains
      ```
   2b. update iguana
       ```
       cd ~
       ./notary
       ```

### Bitcoind process is not running

Check running process bitcoind, to start use command ```bitconind```  

### BTC "is_mine" is not true

Your wallet didn’t import the key. You can not notary. To import use command ``` bitcoin-cli importprivkey 1XXXXXXXXXXX```

### Iguana process is not running

**Warning: All command run into user komodo**
Notarisations doesn’t work, this is very bad.  
Check running process iguana, to start use the command: ```~/notary.sh```

### Wallet size is too big, time to clean it up!

#### Fresh BTC wallet
**Warning: All command run into user komodo**
1. Get Private key, you can skip this, if you know private key
```
priv_key=$(bitcoin-cli dumpprivkey $BTC_ADDRESS)
```
2. Send BTC away, all of it. If you have access to this wallet on the other machine, skip this
```
$ bitcoin-cli sendtoaddress "ADDRESS" {summ}  "" "" true
```
You can get {summ} for transfer use command: ``` bitcoin-cli getbalance ```

We can look address for transfer, on the node to which you want to transfer funds, use commands: ``` echo ${BTC_ADDRESS} ```  

3. Check balances and resend any to 0.00000000 
``` bitcoin-cli getbalance ```

4. Stop processes bitcoind
```
$ bitcoin-cli stop
```

5. Rename wallets
```
$ mv ~/.bitcoin/wallet.dat ~/.bitcoin/wallet.bak-$(date +%Y-%m-%d)
````

6. Start bitcoind 
```
$ bitcoind -daemon
```

7. Import privkeys without triggering rescan
```
$ bitcoin-cli importprivkey "${priv_key}" "" false
```

8. Validateaddress ISMINE=TRUE on both addresses
```
$ bitcoin-cli validateaddress "${BTC_ADDRESS}"
```

9. Send back ALL BTC and some KMD (From other wallet, or from same wallet to this wallet in other PC)
```
$ bitcoin-cli sendtoaddress "1xxx" {summ}  "" "" true
```

#### Fresh KMD wallet
##### Auto freshing
**Warning: All command run into user komodo**

1. Run script ~/scripts/auto_fresh_kmd_wallet.sh
2. Check work komodo, after cleared, use command: ```komodo-cli getbalance ```
3. Check work Iguana, after cleared, use commnad: ``` ps aux | grep iguana ```, you must see ```../agents/iguana```, if process crached, run iguana again, use script ```~/notary```

##### Manual freshing  
Notarisations for KMD and BTC is slow. Use instruction to fresh your wallet:  
**Warning: All command run into user komodo**
1. Get Private key, you can skip this, if you know private key
```
priv_key=$(bitcoin-cli dumpprivkey $KMD_ADDRESS)
```
2. Send KMD away, all of it. If you have access to this wallet on the other machine, skip this
```
$ komodo-cli sendtoaddress "Rxxx" {summ} "" "" true
```

3. Check balances and resend any to 0.00000000 on both wallets

4. Stop processes bitcoind, komodod & iguana
```
$ komodo-cli stop
```

5. Rename both wallets
```
$ mv ~/.komodo/wallet.dat ~/.komodo/wallet.bak-$(date +%Y-%m-%d)
````

6. Start bitcoind and komodod (only with ./komodod no notary gen stuff)
```
$ komodod -daemon
```

7. Import privkeys without triggering rescan
```
$ komodo-cli importprivkey ${priv_key} "" false
```

8. Validateaddress ISMINE=TRUE on both addresses
```
$ komodo-cli validateaddress "Rxxx"
```

9. Send back ALL BTC and some KMD (From other wallet, or from same wallet to this wallet in other PC)
```
$ komodo-cli sendtoaddress "Rxxx" {summ} "" "" true
```

10. When balance > 0 on KMD restart with notary flags
```
$ source ~/komodo/src/pubkey.txt
$ komodod -notary -daemon -pubkey="${pubkey}"
```

11. Check balances on both wallets
```
$ komodo-cli getbalance
```

12. Check wallet sizes, they should be minimal

13. Do fresh UTXO splits
```
$ cd ~/SuperNET/iguana
$ ./acsplit KMD 20
```
 
Wait some time and verify that notarization processes are smooth

#### Fresh other wallet
##### Auto freshing 
**Warning: All command run into user komodo**

1. Open for edit script ~/scripts/auto_fresh_ac_wallet.sh, check value for variable ***maxsize*** , recomended use 20000 for check wallets need clear, and use 7000-8000 for clear wallets.
2. Run script ~/scripts/auto_fresh_ac_wallet.sh


### Update Komodo  

Run command:
    
      ```
      cd ~/komodo
      git pull
      ~/komodotools/webworker01/rebuildkomodo
      ```
      for restart komodo only for KMD
      ```
      komodo-cli stop
      source ~/komodo/src/pubkey.txt
      ./src/komodod  -gen -genproclimit=2 -notary -pubkey="${pubkey}" &
      ```
      for restart komodo for all coins:
      ```
      komodo-cli stop
      cd komodo/src/
      ./fiat-cli stop
      source ~/komodo/src/pubkey.txt
      komodod  -gen -genproclimit=2 -notary -pubkey="${pubkey}" &
      ./assetchains
      ```

### Update Iguana

Run command:  
       ```
       cd ~
       ./notary
       ```
