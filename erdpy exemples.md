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

Deploy :

    erdpy --verbose contract deploy --project=${PROJECT} --recall-nonce --pem=${ALICE} \
          --gas-limit=100000000 \
          --send --outfile="deploy-devnet.interaction.json" --proxy=${PROXY} --chain=${CHAIN_ID} || return

    TRANSACTION=$(erdpy data parse --file="deploy-devnet.interaction.json" --expression="data['emitted_tx']['hash']")

Get address of the deployed smart-contract :

    ADDRESS=$(erdpy data parse --file="deploy-devnet.interaction.json" --expression="data['emitted_tx']['address']")

    erdpy data store --key=address-devnet --value=${ADDRESS}
    erdpy data store --key=deployTransaction-devnet --value=${TRANSACTION}

Issue the NFT *(issueToken)*:  
But first transform token name and token ticker in hexa

    TOKEN_DISPLAY_NAME=0x$(echo -n nft_name | xxd -p)
    TOKEN_TICKER=0x$( echo -n nft_ticker | xxd -p)

    erdpy --verbose contract call ${ADDRESS} --recall-nonce --pem=${ALICE} \
          --gas-limit=100000000 --value=50000000000000000 --function="issueToken" \
          --arguments ${TOKEN_DISPLAY_NAME} ${TOKEN_TICKER} \
          --send --proxy=${PROXY} --chain=${CHAIN_ID}


Set local roles *(setLocalRoles)*:

    erdpy --verbose contract call ${ADDRESS} --recall-nonce --pem=${ALICE} \
          --gas-limit=100000000 --function="setLocalRoles" \
          --send --proxy=${PROXY} --chain=${CHAIN_ID}


Start minting *(startMinting)*:

    erdpy --verbose contract call ${ADDRESS} --recall-nonce --pem=${ALICE} \
          --gas-limit=100000000 --function="startMinting" \
          --send --proxy=${PROXY} --chain=${CHAIN_ID}

Pause minting *(pauseMinting)*:

    erdpy --verbose contract call ${ADDRESS} --recall-nonce --pem=${ALICE} \
          --gas-limit=100000000 --function="pauseMinting" \
          --send --proxy=${PROXY} --chain=${CHAIN_ID}

Set drop *(setDrop)*:

    amount_of_tokens_per_drop=

Unset drop *(unsetDrop)*:

    erdpy --verbose contract call ${ADDRESS} --recall-nonce --pem=${ALICE} \
          --gas-limit=100000000 --function="unsetDrop" \
          --send --proxy=${PROXY} --chain=${CHAIN_ID}

Set new price *(setNewPrice)*

Change base CIDs *(changeBaseCids)*

Set new tokens limit per address *(setNewTokensLimitPerAddress)*

Giveaway *(giveaway)*

Claim smart-contract funds *(claimScFunds)*

Populate indexes *(populateIndexes)*

## for all ##

Mint *(mint)*
    local TOKEN_NAME=0x4e616d65652D3037 # "Name"
    local ROYALTIES=1000 # 10%
    local URI=0x72616e647572692e636f6d # randuri.com
    local SELLING_PRICE=1000000000000000000

    erdpy --verbose contract call ${ADDRESS} --recall-nonce --pem=${ALICE} \
          --gas-limit=50000000 --function="createNft" \
          --arguments ${TOKEN_NAME} ${ROYALTIES} ${URI} ${SELLING_PRICE}  \
          --send --proxy=${PROXY} --chain=${CHAIN_ID}


Shuffle *(shuffle)*:

    erdpy --verbose contract call ${ADDRESS} --recall-nonce --pem=${ALICE} \
          --gas-limit=100000000 --function="shuffle" \
          --send --proxy=${PROXY} --chain=${CHAIN_ID}
