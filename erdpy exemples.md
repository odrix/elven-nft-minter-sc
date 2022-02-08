# erdpy exemples #

Exemples of *erdpy* commands for smart-contract's enpoints.  
Many of this commands need arguments pass in hexadecimal format.  
We use *xxd* commands for conversion.  
   
<br />
First declare some variables :

    ALICE="path_to_PEM"
    ADDRESS=$(erdpy data load --key=address-devnet)
    DEPLOY_TRANSACTION=$(erdpy data load --key=deployTransaction-devnet)
    PROXY=https://devnet-gateway.elrond.com
    CHAIN_ID=D
    PROJECT=ahou1_nft


## only for owner ##

### **NOT TESTED** Deploy : ###

    <!-- TODO  : no good -->
    <!-- erdpy --verbose contract deploy --project=${PROJECT} --recall-nonce --pem=${ALICE} \
          --gas-limit=100000000 \
          --send --outfile="deploy-devnet.interaction.json" --proxy=${PROXY} --chain=${CHAIN_ID} || return -->

    TRANSACTION=$(erdpy data parse --file="deploy-devnet.interaction.json" --expression="data['emitted_tx']['hash']")

Get address of the deployed smart-contract :

    ADDRESS=$(erdpy data parse --file="deploy-devnet.interaction.json" --expression="data['emitted_tx']['address']")

    erdpy data store --key=address-devnet --value=${ADDRESS}
    erdpy data store --key=deployTransaction-devnet --value=${TRANSACTION}

### **NOT TESTED** Issue main collection *(issueToken)*: ###
But first transform token name and token ticker in hexa.

    TOKEN_DISPLAY_NAME=0x$(echo -n nft_name | xxd -p)
    TOKEN_TICKER=0x$( echo -n nft_ticker | xxd -p)

    erdpy --verbose contract call ${ADDRESS} --recall-nonce --pem=${ALICE} \
          --gas-limit=100000000 --value=50000000000000000 --function="issueToken" \
          --arguments ${TOKEN_DISPLAY_NAME} ${TOKEN_TICKER} \
          --send --proxy=${PROXY} --chain=${CHAIN_ID}


### Set local roles *(setLocalRoles)* : ###

    erdpy --verbose contract call ${ADDRESS} --recall-nonce --pem=${ALICE} \
          --gas-limit=100000000 --function="setLocalRoles" \
          --send --proxy=${PROXY} --chain=${CHAIN_ID}


### Start minting *(startMinting)* : ###

    erdpy --verbose contract call ${ADDRESS} --recall-nonce --pem=${ALICE} \
          --gas-limit=100000000 --function="startMinting" \
          --send --proxy=${PROXY} --chain=${CHAIN_ID}

### Pause minting *(pauseMinting)* : ###

    erdpy --verbose contract call ${ADDRESS} --recall-nonce --pem=${ALICE} \
          --gas-limit=100000000 --function="pauseMinting" \
          --send --proxy=${PROXY} --chain=${CHAIN_ID}

### Set drop *(setDrop)* : ###  
Exemple for 12 tokens per drop.

    AMOUNT_OF_TOKENS_PER_DROP=12

    erdpy --verbose contract call ${ADDRESS} --recall-nonce --pem=${ALICE} \
          --gas-limit=100000000 --function="setDrop" \
          --arguments ${AMOUNT_OF_TOKENS_PER_DROP} --send --proxy=${PROXY} --chain=${CHAIN_ID}

### Unset drop *(unsetDrop)* : ###

    erdpy --verbose contract call ${ADDRESS} --recall-nonce --pem=${ALICE} \
          --gas-limit=100000000 --function="unsetDrop" \
          --send --proxy=${PROXY} --chain=${CHAIN_ID}

### Set new price *(setNewPrice)* :  ###
Exemple price for 0.7 egld. If use an integer price then remove '::-2' in arguments.
  
    NEW_PRICE=$(echo "0.7*1000000000000000000" | bc)

    erdpy --verbose contract call ${ADDRESS} --recall-nonce --pem=${ALICE} \
          --gas-limit=100000000 --function="setNewPrice" \
          --arguments ${NEW_PRICE::-2} --send --proxy=${PROXY} --chain=${CHAIN_ID}


### **NOT TESTED** Change base CIDs *(changeBaseCids)* : ###

    IMAGE_BASE_CID=0x$( echo -n url_image_base_cid | xxd -p)  
    METADATA_BASE_CID=0x$( echo -n url_metadata_base_cid | xxd -p)  

    erdpy --verbose contract call ${ADDRESS} --recall-nonce --pem=${ALICE} \
          --gas-limit=100000000 --function="changeBaseCids" \
          --arguments ${IMAGE_BASE_CID} ${METADATA_BASE_CID} --send --proxy=${PROXY} --chain=${CHAIN_ID}

### Set new tokens limit per address *(setNewTokensLimitPerAddress)* : ###
Exemple for limit of 5.   

    LIMIT=05  

    erdpy --verbose contract call ${ADDRESS} --recall-nonce --pem=${ALICE} \
          --gas-limit=100000000 --function="setNewTokensLimitPerAddress" \
          --arguments ${LIMIT} --send --proxy=${PROXY} --chain=${CHAIN_ID}

### **NOT TESTED** Giveaway *(giveaway)* : ###

  <!-- TODO  -->

### **NOT TESTED** Claim smart-contract funds *(claimScFunds)* : ###

    erdpy --verbose contract call ${ADDRESS} --recall-nonce --pem=${ALICE} \
          --gas-limit=100000000 --function="claimScFunds" \
          --send --proxy=${PROXY} --chain=${CHAIN_ID}

### Populate indexes *(populateIndexes)* : ###

    AMOUNT=100

    erdpy --verbose contract call ${ADDRESS} --recall-nonce --pem=${ALICE} \
          --gas-limit=100000000 --function="populateIndexes" \
          --arguments ${AMOUNT} --send --proxy=${PROXY} --chain=${CHAIN_ID}

## for all ##

### Mint *(mint)* : ###
Exemple for a price of 0.6 egld, and minting 2 NFT.   
If use an integer price then remove '::-2' in arguments.  

    UNIT_PRICE=0.6
    QTY=02
    TOTAL_PRICE=$(echo "$QTY*$UNIT_PRICE*1000000000000000000" | bc)

    erdpy --verbose contract call ${ADDRESS} --recall-nonce --pem=${ALICE} \
          --gas-limit=50000000 --value=${TOTAL_PRICE::-2} --function="mint" \
          --arguments ${QTY} --send --proxy=${PROXY} --chain=${CHAIN_ID}


### Shuffle *(shuffle)* : ###

    erdpy --verbose contract call ${ADDRESS} --recall-nonce --pem=${ALICE} \
          --gas-limit=100000000 --function="shuffle" \
          --send --proxy=${PROXY} --chain=${CHAIN_ID}


