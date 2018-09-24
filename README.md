# Documentation

Table of contents
=================

<!--ts-->
   * [small count UTXO](#small-count-utxo)
   * [Balance is smaller](#balance-is-smaller)
   * [Installation](#installation)
   * [Usage](#usage)
      * [STDIN](#stdin)
      * [Local files](#local-files)
      * [Remote files](#remote-files)
      * [Multiple files](#multiple-files)
      * [Combo](#combo)
      * [Auto insert and update TOC](#auto-insert-and-update-toc)
   * [Tests](#tests)
   * [Dependency](#dependency)
<!--te-->

### small count UTXO
To split UTXO use a command:

```
$ ./acsplit {coin_name} 50
```
if you receive error :```{"error":"couldnt create duplicates tx","tag":"xxxxxxxxx"}```, send 0.1 funds from your wallet to same address, wait confirmations
(you can use command  to check confirmation ```komodo-cli -ac_name={coin_name} listtransactions```), after confirmation run the command for splitting again

### Balance is smaller <0.2
When your balance comes to the critical it’s time to think about topping it up

### balance is very small <0.05

Your balance is very small, If you do not fill it urgently, you will lose notarisations for this fund

### Last notary BTC was more than 3 hours ago
If your node wasn’t notarised more than 3 hours ago there can be some problems

### Bitcoind process is not running

Check running process bitcoind, to start use command ```bitconind```  

### BTC "is_mine" is not true

Your wallet didn’t import the key. You can not notary. To import use command ``` bitcoin-cli importprivkey 1XXXXXXXXXXX```

### Iguana process is not running

Notarisations doesn’t work, this is very bad.  
Check running process iguana, to start use the command: ```~/notary.sh```

### Wallet size is too big, time to clean it up!

Notarisations for KMD and BTC is slow. Use instruction to fresh your wallet:
1. Send BTC away, all of it. If you have access to this wallet on the other machine, skip this
```
$ bitcoin-cli sendtoaddress "1xxx" {summ}  "" "" true
```

2. Send KMD away, all of it. If you have access to this wallet on the other machine, skip this
```
$ komodo-cli sendtoaddress "Rxxx" {summ} "" "" true
```

3. Check balances and resend any to 0.00000000 on both wallets

4. Stop processes bitcoind, komodod & iguana
```
$ pkill -15 iguana
$ bitcoin-cli stop
$ komodo-cli stop
```

5. Rename both wallets
```
$ mv ~/.komodo/wallet.dat ~/.komodo/wallet.dat.BIG
$ mv ~/.bitcoin/wallet.dat ~/.bitcoin/wallet.dat.BIG
````

6. Start bitcoind and komodod (only with ./komodod no notary gen stuff)
```
$ bitcoind -daemon
$ komodo -daemon
```

7. Import privkeys without triggering rescan
```
$ bitcoin-cli importprivkey "Kxxx" "" false
$ komodo-cli  importprivkey "Uxxx" "" false
```

8. Validateaddress ISMINE=TRUE on both addresses
```
$ bitcoin-cli validateaddress "1xxx"
$ komodo-cli validateaddress "Rxxx"
```

9. Send back ALL BTC and some KMD (From other wallet, or from same wallet to this wallet in other PC)
```
$ bitcoin-cli sendtoaddress "1xxx" {summ}  "" "" true
$ komodo-cli sendtoaddress "Rxxx" {summ} "" "" true
```

10. When balance > 0 on KMD restart with notary flags
```
$ komodod -notary -daemon -pubkey="xxxxxxxx"
```

11. Check balances on both wallets
```
$ komodo-cli getbalance
$ bitcoin-cli getbalance
```

12. Check wallet sizes, they should be minimal

13. Start iguana, dpow
```
$ cd ~/SuperNET/iguana
$ git checkout dev && git pull && ./m_notary 
$ cd ~/komodo/src && ./dpowassets
```

14. Do fresh UTXO splits
```
$ cd ~/SuperNET/iguana
$ ./acsplit KMD 50
$ ./acsplit BTC 30
```
 
Wait some time and verify that notarization processes are smooth

### Balance on node, is not growing for more then 2 hours (check mining)

Balance in your node is not growing, check running process mining, if process running, you can check log file ~/.komodo/debug.log

### Did not mine block for more then 2.5h in AR node (check mining!)

Your node is not mining new blocks more then 2.5h, check running process mining, if process running, you can check log file ~/.komodo/debug.log

### Count of komodod processes != 2

One or more process komodod is not running for mining. Please check all mining processes.
