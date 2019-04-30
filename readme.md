# Creating new coin from litecoin

Corresponding Repository: https://github.com/panchbhai1969/git_coin
Coin created from : https://github.com/litecoin-project/litecoin
There are 2 folders here one contains the whole code of the new coin and another one is the files in which changes were done. Us the description below to get an understanding of the changes done.


The new currency has the following  specifications: (11640160 is my roll number and 6th semester is going on , these aspect make this coin unique to me: Anand Panchbhai, Roll number: 11640160)
- Time stamp on the genesis coin : 11640160 Semester 6
- Block Reward: 20 
- Reward halves after : 10k blocks
- Difficulty adjusted after : 3.5 days
- New blocks generated in :  5 minutes
- Max number of coins:  350000 coins

How to run (Will have to perform once for server and once for the client on different machines):
- Go to the directory mycoin
- go to the directory src
- run ``` make -f makefile.unix ```
- Once the file is made run ``` ./litecoind ```
- To build the interface go to the mycoin directory and run 
	``` qmake ```

Once you have successfully run the steps mentioned above:
- A file .litecoin will be generated in the home of your unix machine
- open the .litecoin folder
- create a new file named litecoin.conf
- If you want to run a server write the following in the conf file:
	```
		server=1
		rpsuser=user 
		rpcpassword[password
	```
- If you want to run a client write the following in the conf file:
	```
		addnode="<IP of the server node>"
-

Go to the mycoin folder of the respective server and client and run ./litecoin-qt
The GUI will fireup.

To start mining on the client: go to the help->debug log -> command-> enter command ``` setgenerate true ```

Dependencies : You can find the dependencies in the doc folder of the mycoin folder under the file for unix documentation.

# The Changes made in the files campared to original(These changes) :

## Fixing Depricated code

Line number 242 :

Orignal :
```
 const CScriptID& hash = boost::get<const CScriptID&>(address);
```

Changed:
```
 const CScriptID& hash = boost::get<CScriptID>(address);
```

## Changing the network parameters

### RPC port and peer-peer ports

RPC - port is used by miners to transmit the mining data
Peer-to-Peer - Port is used by the clients to talk to other clients

We are trying to isolate our coins to a different specifications so thet it does not collide with other crypto currencies. I have choose 2333 and 2332, you can use any port you like, make sure you are not using ports commonly used by other standars software. Run this command on the mycoin directory on the command line.

'''
find . -type f -print0 | xargs -0 sed -i 's/9333/2333/g'
find . -type f -print0 | xargs -0 sed -i 's/9332/2332/g'
'''

### Public address identifiers

We will be changing mycoin/src/base58.h
Line number 275

Orignal:
```
{
        PUBKEY_ADDRESS = 48, // Litecoin addresses start with L 
        SCRIPT_ADDRESS = 5,
        PUBKEY_ADDRESS_TEST = 111,
        SCRIPT_ADDRESS_TEST = 196,
    };
```

Changed:
```
{
        PUBKEY_ADDRESS = 35, // Litecoin addresses start with F 
        SCRIPT_ADDRESS = 5,
        PUBKEY_ADDRESS_TEST = 95,
        SCRIPT_ADDRESS_TEST = 196,
    };
```
48 Corresponds to L, I changes it to 35 which corresponds to F.
You can get the corresponding numbers here: https://en.bitcoin.it/wiki/List_of_address_prefixes

### Removing the alert system

Deprecated system asking for updating the client if not upto date. To prevent that from happening we will generate a new set of kety pairs:

Go to your home directory and run the following on your terminal:
```
openssl ecparam -genkey -name secp256k1 -out alertkey.pem
openssl ec -in alertkey.pem -text > alertkey.hex
openssl ecparam -genkey -name secp256k1 -out testnetalert.pem
openssl ec -in testnetalert.pem -text > testnetalert.hex
openssl ecparam -genkey -name secp256k1 -out genesiscoinbase.pem
openssl ec -in testnetalert.pem -text > genesiscoinbase.hex
```
-----------------------------------------
Open alert.cpp

Line 22

Orignal
```
static const char* pszMainKey = "040184710fa689ad5023690c80f3a49c8f13f8d45b8c857fbcbc8bc4a8e4d3eb4b10f4d4604fa08dce601aaf0f470216fe1b51850b4acf21b179c45070ac7b03a9";
static const char* pszTestKey = "041dffea20268866c39e00dab0eaaa159ef6830089cd25580f4125a62bc8d9ccb0f63f492c35730a8a03cd475b7a7ac57e914aa543dc744f7dc5734cb2cc5ba77b";

```

Changed:
```
static const char* pszMainKey = "<your key from pub key of alertkey.hex, remember to remove the colon in between the numbers>";
static const char* pszTestKey = "<your key from pub key of testnetalert.hex, remember to remove the colon in between the numbers>";
```
run ```cat alertkey.hex``` of the desktop to get the info about the generated key, similarly for testnetalert.hex
-------------------------------------------
Open mycoin/src/main.cpp 

line number: 2788

Orignal
```
txNew.vout[0].scriptPubKey = CScript() << ParseHex("041dffea20268866c39e00dab0eaaa159ef6830089cd25580f4125a62bc8d9ccb0f63f492c35730a8a03cd475b7a7ac57e914aa543dc744f7dc5734cb2cc5ba77b") 
```

Changed
```
txNew.vout[0].scriptPubKey = CScript() << ParseHex("<Use the same key you obtained in the previous step.>") 

```
Doing this prevents your coin to get connected to other networks.

### Change  bytes

Open mycoin/src/main.cpp 
Line number: 2745

Orignal:
```
if (fTestNet)
    {
        pchMessageStart[0] = 0xf1;
        pchMessageStart[1] = 0xc2;
        pchMessageStart[2] = 0xb3;
        pchMessageStart[3] = 0xd4;
        hashGenesisBlock = uint256("0x<some hash>");
    }
```

Change the last digits of these numbers, the reason of changing them is same as that of all the changes in this section. i.e. to prevent them from interacting with the rest of the  
Changed:
```
if (fTestNet)
    {
        pchMessageStart[0] = 0xf6;
        pchMessageStart[1] = 0xc7;
        pchMessageStart[2] = 0xb8;
        pchMessageStart[3] = 0xd9;
        hashGenesisBlock = uint256("0x<some hash>");
    }
```

Line number : 3125

Orignal
```
unsigned char pchMessageStart[4] = { 0xf9, 0xc8, 0xb7, 0xd6 }; // Litecoin: increase each by 
```
Changed:
```
unsigned char pchMessageStart[4] = { 0xf1, 0xc8, 0xb7, 0xd6 }; // Litecoin: increase each by
```
Similar changes to the previous case.

### Remove hardcoded network information

Seed nodes


Hard coded IP addresses (DNS Seeds)

Open mycoin/src/net.cpp 

Line number: 1178

Change it to :
```
static const char *strMainNetDNSSeed[][2] = {

    {NULL, NULL}
};

static const char *strTestNetDNSSeed[][2] = {
   
    {NULL, NULL}
};

```
Line number 1228

Change it to:

```

unsigned int pnSeed[] = { 0x0 };

``` 
Remove all hardcoded IP addresses

## Chaging the Genesis block

### Changing the block reward (20)
Open mycoin/src/main.cpp 
Line number 1090

From 50 to 20
Orignal :

```
int64 nSubsidy = 50 * COIN;
```

Changed:

```
int64 nSubsidy = 20 * COIN;
```
## Changing the number of blocks after which the reward is made to half(10000)
Open mycoin/src/main.cpp 
Line number 1093

Original:
```
nSubsidy >>= (nHeight / 840000); // Litecoin: 840k blocks in ~4 years
```

Changed:
```
nSubsidy >>= (nHeight / 10000); // Litecoin: 10k blocks
```

## Difficulty retarget period
Basically when the target is adjusted to match the difficulty that can be handled by the miners to maintain the expected rate of block generation. For our coin it is 1 day here.
Open mycoin/src/main.cpp 
Line number 1098

Original:

```
static const int64 nTargetTimespan = 3.5 * 24 * 60 * 60; // Litecoin: 3.5 days

```

Changed:

```
static const int64 nTargetTimespan = 1 * 24 * 60 * 60; // Litecoin: 1 day
```
## Time between block generation

Open mycoin/src/main.cpp 
Line number 1099

Changing the time required for one block generation, this is used while adjusting the difficulty level. For bitcoin it is 10 minutes. For our coin it is 5 minutes here.

Original:
```
static const int64 nTargetSpacing = 2.5 * 60; // Litecoin: 2.5 minutes
```

Changed:
```
static const int64 nTargetSpacing = 5 * 60; // Litecoin: 5 minutes
```

## Change max money variable
Open mycoin/src/main.h 
Line number 55
This will change the total number of coins that can be generated in our coin: for litecoin it is 84000000 for our coin it will be 350000. It is calculated in the following manner:

Sigma(10000(Number of blocks after reward is halved)*20(coin reward)*(1/2)^n)
n=number of times halfing takes place.

Original
```
static const int64 MAX_MONEY = 84000000 * COIN;
```
Changed
```
static const int64 MAX_MONEY = 350000 * COIN;
```
## Coin Matirity period

Original:
```
return dPriority > COIN * 576 / 250;
```

Line number: 627
Number of blocks after which the mined coins can be used for transaction, in our case the number of block will be 30. 20 is added at a later part of the code. We want the maturity period of our coin to be 30 thus 10 is the value we give here.
 
Original
```
static const int COINBASE_MATURITY = 100;
```

Changed
```
static const int COINBASE_MATURITY = 10;
```

Line number 627:

Difficulty adjustment after 288 coins.

Original:
```
return dPriority > COIN * 576 / 250;
```

Changed:
```
return dPriority > COIN * 288 / 250;
```

## Changes in the chain parameters

Open mycoin/src/main.cpp 
Line number 2794

Original:
```
txNew.vout[0].nValue = 50 * COIN;
```

Put your block reward instead of 50 here.

Changed:
```
txNew.vout[0].nValue = 20 * COIN;
```

### Change the time stamp : 
Line number: 2782

Changed to:
```
 const char* pszTimestamp = "11640160 Semester 6";
```

### Change Genesis time (Epic Time)

Use terminal to get epic time by 
``` data +%s ```
Line number: 2794
Change to:
```
block.nTime    = <Value obtained from the terminal>;
```
Line number: 2800
Use terminal to get epic time by 
``` data +%s ```
Change to:
```
 block.nTime    = 1556535060;
```

## Change the merkel root using the debug functionality

COmpile the code by being in the src folder using the following command:
``` make -f makefile.unix ```
After the compilation completes, run ```./litecoind```. An error will be encountered relaed to assertions. Go to ~/.litecoin/debug.log and copy the hash in the last line after the colon. This is your merkel hash:

Line number 2852:

```
assert(block.hashMerkleRoot == uint256("<Merkel hash from the dubug file>"));
```

## Change the hash of the genesis coin

Add the following code to the line number 2798:

```
if (true && block.GetHash() != hashGenesisBlock)
        {
            printf("Searching for genesis block...\n");
            // This will figure out a valid hash and Nonce if you're
            // creating a different genesis block:
            uint256 hashTarget = CBigNum().SetCompact(block.nBits).getuint256();
            uint256 thash;
            char scratchpad[SCRYPT_SCRATCHPAD_SIZE];
 
            loop
            {
#if defined(USE_SSE2)
                // Detection would work, but in cases where we KNOW it always has SSE2,
                // it is faster to use directly than to use a function pointer or conditional.
#if defined(_M_X64) || defined(__x86_64__) || defined(_M_AMD64) || (defined(MAC_OSX) && defined(__i386__))
                // Always SSE2: x86_64 or Intel MacOS X
                scrypt_1024_1_1_256_sp_sse2(BEGIN(block.nVersion), BEGIN(thash), scratchpad);
#else
                // Detect SSE2: 32bit x86 Linux or Windows
                scrypt_1024_1_1_256_sp(BEGIN(block.nVersion), BEGIN(thash), scratchpad);
#endif
#else
                // Generic scrypt
                scrypt_1024_1_1_256_sp_generic(BEGIN(block.nVersion), BEGIN(thash), scratchpad);
#endif
                if (thash <= hashTarget)
                    break;
                if ((block.nNonce & 0xFFF) == 0)
                {
                    printf("nonce %08X: hash = %s (target = %s)\n", block.nNonce, thash.ToString().c_str(), hashTarget.ToString().c_str());
                }
                ++block.nNonce;
                if (block.nNonce == 0)
                {
                    printf("NONCE WRAPPED, incrementing time\n");
                    ++block.nTime;
                }
            }
            printf("block.nTime = %u \n", block.nTime);
            printf("block.nNonce = %u \n", block.nNonce);
            printf("block.GetHash = %s\n", block.GetHash().ToString().c_str());
        }
```
Change line number 38:

Change:
```
uint256 hashGenesisBlock("<Hash of the genesis from running the code after compiling and running as in a previous section from the debug log>");

```
Line number 2796:
Change:
```
block.nNonce   = <Nounce from the debug log>;
```

Set the condition to false in line number: 2798 

```
if (false && block.GetHash() != hashGenesisBlock)

```

## Change the checkpoits

Open mycoin/src/checkpoints.cpp 

Remove all checkpoints except your genesis coin and use the same hash here.

Change it to:
```
static MapCheckpoints mapCheckpoints =
        boost::assign::map_list_of
        (  0, uint256("0xd8d7724d78f3129932482971a080e25c3b5c6d5a26953303e7c821d084d6bd02"))
        
        ;
    static const CCheckpointData data = {
        &mapCheckpoints,
        <Timestampt (epic time from the previous change)>, // * UNIX timestamp of last checkpoint block
        0,    // * total number of transactions between genesis and last checkpoint 
                    //   (the tx=... number in the SetBestChain debug.log lines)
        1.0     // * estimated number of transactions per day after checkpoint
    };
```

0 in the second one as genesis is the last checkpoint in our case, 1 is kept arbitrarily.

All changes in the files have been made now you can run the instructions in the top of this readme to run the files.

------------------------------------------------------------------------------







