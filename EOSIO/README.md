STEPS TO BUILD AND DEPLOY EOSIO TOKEN SMART CONTRACT :

1. Install binaries

	1.1 wget https://github.com/eosio/eos/releases/download/v1.5.0/eosio_1.5.0-1-ubuntu-18.04_amd64.deb
	1.2 sudo apt install ./eosio_1.5.0-1-ubuntu-18.04_amd64.deb

2. Setup a development directory, stick to it.

	2.1 mkdir contracts
	2.2 cd contracts

3. Get the path of that directory

	3.1 pwd  //remember it

4. Boot Node and Wallet

	4.1 keosd &
	
	4.2 nodeos -e -p eosio \
    	--plugin eosio::producer_plugin \
    	--plugin eosio::chain_api_plugin \
    	--plugin eosio::http_plugin \
    	--plugin eosio::history_plugin \
    	--plugin eosio::history_api_plugin \
    	--data-dir /home/varsha/Varsha/contracts/eosio/data \
    	--config-dir /home/varsha/Varsha/contracts/eosio/config \
    	--access-control-allow-origin='*' \
    	--contracts-console \
    	--http-validate-host=false \
    	--verbose-http-errors \
    	--filter-on='*' >> nodeos.log 2>&1 &
	
	4.3 tail -f nodeos.log
	4.4 cleos wallet list
	4.5 curl http://localhost:8888/v1/chain/get_info

5. Install the CDT

	5.1 wget https://github.com/EOSIO/eosio.cdt/releases/download/v1.4.1/eosio.cdt-1.4.1.x86_64.deb

	5.2 sudo apt install ./eosio.cdt-1.4.1.x86_64.deb

6. Install from Source

	6.1 cd /home/varsha/Varsha/contracts  /// address from step 3.1

7. Download

	7.1 git clone --recursive https://github.com/eosio/eosio.cdt --branch v1.4.1 --single-branch

	7.2 cd eosio.cdt

8. Build

	8.1 ./build.sh

	8.2 sudo ./install.sh

9. Create a Wallet

	9.1 cleos wallet create --to-console  //this will return password save it.

	9.2 cleos wallet open   //open wallet

	9.3 cleos wallet list   //list of wallets

	9.4 cleos wallet unlock  //unlock wallet

10. Import keys into your wallet

	10.1 cleos wallet create_key    //Generate private key and return public key

11. Import the Development Key

	11.1 cleos wallet import  ///You'll be prompted for a private key, enter the eosio development key : 5KQwrPbwdL6PhXujxW37FSSQZ1JiwsST4cqQzDeyXtP79zkvFD3


12. Create Test Accounts

	12.1 cleos create account eosio bob EOS6f9kP1pyk4TD8KpGXtk7tj2V1L4AUxQ5Vbk53vStSCLhcv1ZHP    //created account bob

	12.2 cleos create account eosio alice EOS6f9kP1pyk4TD8KpGXtk7tj2V1L4AUxQ5Vbk53vStSCLhcv1ZHP  //created account alice

	12.3 cleos get account bob    ///Check which public key is associate to alice


13. Create Smart Contracts

	13.1 touch hello.cpp    /// create new file hello.cpp

	13.2 Write hello world contract using cpp:

		#include <eosiolib/eosio.hpp>
		#include <eosiolib/print.hpp>

		using namespace eosio;

		class hello : public contract {
  		public:
      			using contract::contract;

      			[[eosio::action]]
      			void hi( name user ) {
         			print( "Hello, ", user);
      			}
		};

		EOSIO_DISPATCH( hello, (hi))

	13.3 Compile above code:

		eosio-cpp -o hello.wasm hello.cpp --abigen

	13.4 cleos wallet keys

	13.5 cleos create account eosio hello EOS6f9kP1pyk4TD8KpGXtk7tj2V1L4AUxQ5Vbk53vStSCLhcv1ZHP -p eosio@active  //create account for contract

	13.6 cleos set contract hello /home/varsha/Varsha/contracts/hello -p hello@active   // Broadcast the compiled wasm to the blockchain

	13.7 cleos push action hello hi '["bob"]' -p bob@active //Push action to it

	13.8 cleos push action hello hi '["bob"]' -p alice@active

	13.9 In this case "alice" is the one who authorized it and user is just an argument. Modify the contract so that the authorizing user, "alice" in this case, must be the same as the user the contract is responding "hi" to. Use the require_auth method. This method takes an name as a parameter, and will check if the user executing the action matches the provided paramter.

	void hi( name user ) {

   		require_auth( user );

   		print( "Hello, ", name{user} );

	}

	Recompile the contract: eosio-cpp -o hello.wasm hello.cpp --abigen

	Then,update it : cleos set contract hello /home/varsha/Varsha/contracts/hello -p hello@active

	Type command: cleos push action hello hi '["alice"]' -p alice@active

14. Deploy, Issue and Transfer Tokens

	14.1 Obtain Contract Source

	cd /home/varsha/Varsha/contracts

	14.2 git clone https://github.com/EOSIO/eosio.contracts --branch v1.4.0 --single-branch  ///pull source

	14.3 cd eosio.contracts/eosio.token  ///navigate to directory

	14.4 Create Account for Contract

	cleos create account eosio eosio.token EOS6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV

	14.5 Compile the Contract

	eosio-cpp -I include -o eosio.token.wasm src/eosio.token.cpp --abigen

	14.6 Deploy the Token Contract

	cleos set contract eosio.token /home/varsha/Varsha/contracts/eosio.contracts/eosio.token --abi eosio.token.abi -p eosio.token@active

	14.7 Create the Token

	cleos push action eosio.token create '[ "eosio", "1000000000.0000 SYS"]' -p eosio.token@active

	14.8 Issue Tokens

	cleos push action eosio.token issue '[ "alice", "100.0000 SYS", "memo" ]' -p eosio@active

	14.9 To inspect the transaction, try using the -d -j options, they indicate "don't broadcast" and "return transaction as json," which you may find useful during development.

	cleos push action eosio.token issue '["alice", "100.0000 SYS", "memo"]' -p eosio@active -d -j

15. Transfer Tokens

cleos push action eosio.token transfer '[ "alice", "bob", "25.0000 SYS", "m" ]' -p alice@active

	15.1 check if bob got the token

	cleos get currency balance eosio.token bob SYS

	15.2 Check "alice's" balance, notice that tokens were deducted from the account

	cleos get currency balance eosio.token alice SYS



DEPLOY AND RUN SMART CONTRACT ON EOS TESTNET:

1. Install binaries and CDT. Follow above given binaries installation steps.
2. Write Smart Contract "hello.cpp". Follow above given steps of writing smart contract.
3. Compile Smart Contract by using: 
	eosio-cpp -o hello.wasm hello.cpp --abigen
4. Fire up your terminal and navigate to your directory that you saved the hello.cpp file. In my case 
	cd /home/varsha/Varsha/contractstest/hello 
5. Create EOS account on testnet. I create on https://dev4eos.com. It will return JSON string:
	{ "Msg": "succeeded", "keys": { "active_key": { "public", "EOS8GLTBjeKcUjpdgXpFZ9j3mke5tkjFUjaX2yLrMQvCJUbmCH2yj", 
	"private", "5K3ygQFKAyzXsKDw3U3Mhssqyh6GCdGhkF5WFCXjxZ48ZSUZapV"}, "owner_key": { "public", "EOS8gyTni5gW9ARtnYxKBpxX94dZMXTa7yYBsUvopeDG2tXUtpCfm", " private ":" 5Kix3SeySDs3XKrqy93zt1mbAwHADApUQxCowrdggs8q1Sjgney "}},
" account ":" suniltestacc "}

	Save this string somewhere. You will need keys to deploy Smart Contract.

6. To confirm your account is created:
	cleos -u https://api.kylin.alohaeos.com:443 get account suniltestacc

7. Create wallet:
	cleos wallet create -n varshwallet --to-console 
	This will return password.Save this so that this will be used to unlock wallet in future.
8. Unlock the wallet:
	cleos wallet unlock -n varshwallet
	This will ask password, enter the above password.
9. You can see the walletis unlocked or not by using:
	cleos wallet list
	This will return wallet list. The wallet which is unlocked will be displayed with "*"

10. Open up the JSON I asked you to save earlier. Try to locate your “active_key” and look for the private field inside it’s brackets that looks like this:{“private”:“5KFeE7nZgudaNKiFZfybtT4hneKLQiw1HSeJA5BYGoeYVrwTADs”}, and import Private key on wallet:
	cleos wallet import -n varshwallet --private-key 5KFeE7nZgudaNKiFZfybtT4hneKLQiw1HSeJA5BYGoeYVrwTADs

11. Deploy EOS Smart Contract:

	11.1 Before deploying make sure your wallet is unlocked.Then set contract.Replace /home/varsha/Varsha/contractstest/hello with your hello.wasm directory path.

	cleos -u https://api.kylin.alohaeos.com set contract suniltestacc /home/varsha/Varsha/contractstest/hello 

	This will return :

	Reading WASM from /home/varsha/Varsha/contractstest/hello/hello.wasm...
Publishing contract...
executed transaction: c5f17ed57878585c36935cf5ca116a6456e990846468bdf19472a00b4936d389  1456 bytes  288 us
#         eosio <= eosio::setcode               {"account":"suniltestacc","vmtype":0,"vmversion":0,"code":"0061736d0100000001390b60027f7e006000017f6...
#         eosio <= eosio::setabi                {"account":"suniltestacc","abi":"0e656f73696f3a3a6162692f312e31000102686900010475736572046e616d65010...

	11.2 If you get error the error of insufficent RAM, you need ram to deploy. Use command to buy RAM:

	cleos -u https://api.kylin.alohaeos.com system buyram suniltestacc suniltestacc --kbytes 1000

	11.3 again execute 11.1 step command after you buy ram otherwise go to next step.

EXECUTING OUR EOS SMART CONTRACT:

1. Now that we have our baby deployed and alive on the blockchain, let’s go ahead and execute the hi method on our smart contract.

	cleos -u https://api.kylin.alohaeos.com push action suniltestacc hi '["bobmarley"]' -p suniltestacc@active

	This will return transaction hash:

	executed transaction: 66384f68c8bf2a8ff8a3e49efe59a0f023ea8e43449dc40393b18a5d0c4ab059  104 bytes  159 us
	#  suniltestacc <= suniltestacc::hi             {"user":"bobmarley"}




