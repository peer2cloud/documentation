# Documentation

### small count UTXO
For split to UTXO use command:

```
$ ./acsplit {coin_name} 50
```
if you get error :```{"error":"couldnt create duplicates tx","tag":"xxxxxxxxx"}```, send 0.1 funds from your wallet to same address, wait confimations
(you can check use command ```komodo-cli -ac_name={coin_name} listtransactions```), and again run command for split

### Balance is small <0.2
You balance, is approaching the critical, it's time to think about how to replenish it

### balance is very small <0.05

You balance is very small, If you do not fill it urgently, you will lose notarisations for this fund

### Last notary BTC was more than 3 hours ago

Your node, not notarised more than 3 hours ago, there may be some problems

### Bitcoind process is not running

Check running proccess bitcoind, for start use ```bitconind```  

### BTC "is_mine" is not true

For you wallet not import key. You can not notary. 

### Iguana process is not running

Notarisations not working, this very bad.  
Check running process iguana, for start use command: ```~/notary.sh```

### Wallet size is too big, time to cleanup!

Notarisations for KMD and BTC is slow. Use instruction for fresh you wallet:
1. Send BTC away, all of it. If you have access to this wallet other machine, skip this
```
$ bitcoin-cli sendtoaddress "1xxx" {summ}  "" "" true
```

2. Send KMD away, all of it. If you have access to this wallet other machine, skip this
```
$ komodo-cli sendtoaddress "Rxxx" {summ} "" "" true
```

3. Check balances and resend any until 0.00000000 on both wallets

4. Stop bitcoind, komodod & iguana
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

12. Check wallet sizes, should be minimal

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
 
Wait some time and verify notarizations are smooth


### Balance on node, is not growing for more then 2 hours (check mining)

Balance in your node not growing, check running process mining, if process running, you can check log file ~/.komodo/debug.log

### Did not mine block for more then 2.5h in AR node (check mining!)

You node not minre new blocks more 2.5h, check running process mining, if process running, you can check log file ~/.komodo/debug.log

### Count of komodod processes != 2

One or more process komodod for mining not running. Please check all mining procees.