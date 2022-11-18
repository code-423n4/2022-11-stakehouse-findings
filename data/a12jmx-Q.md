1.

It is best practice to always have the licensing at on line 1 and the pragma on line 2 or have a space and then have the pragma on line 3.
This is not the case with some contracts.
Consider changing the license instead of the pragma to line 1 and follow best practice guidelines further as stated above in the contracts:

	OptionalHouseGatekeeper.sol
	SavETHVaultDeployer.sol
	LPTokenFactory.sol
	GiantLP.sol
	LPToken.sol
	SyndicateFactory.sol
	GiantPoolBase.sol
	LSDNFactory.sol
	GiantSavETHVaultPool.sol
	GiantMevAndFeesPool.sol
	Syndicate.sol
	SyndicateErrors.sol

2.

Grammer issues in comments in:

2.1

Contract: LPToken.sol

2.1.1

	line 50

	"a" should be "an"
	
Modified Comment:

	/// @notice Allows an LP token owner to burn their tokens

2.2

Contract: SyndicateFactory.sol

2.2.1

	line 41

	"Off chain" should be hyphenated as "Off-chain"
	
Modified Comment:

	// Off-chain logging of all deployed instances from this factory

2.3

Contract: GiantPoolBase.sol

2.3.1

	lines 50, and 66

	"choose" is misspelled as "chose"
	
Modified Comments:

	/// @notice Allow a user to choose to burn their LP tokens for ETH only if the requested amount is idle and available from the contract

	/// @notice Allow a user to choose to withdraw vault LP tokens by burning their giant LP tokens. 1 Giant LP == 1 vault LP

2.3.2

	line 99

	"post depositing" should be hyphenated as "post-depositing"
	
Modified Comment:

	/// @dev Allow an inheriting contract to have a hook for performing operations post-depositing ETH

2.4

Contract: GiantSavETHVaultPool.sol

2.4.1

	line 41

	"a" should be "an"
	
Modified Comment:

	// For every vault specified, supply ETH for at least 1 BLS public key of an LSDN validator

2.4.2

	line 76

	consider changing "Firstly" to "First" for better readability
	
Modified Comment:

	// First capture current dETH balance and see how much has been deposited after the loop

2.5

Contract: OwnableSmartWallet.sol

2.5.1

	line 123

	"owner" should be "owner's"
	
Modified Comment:

	/// @param from The owner's address

2.6

Contract: SavETHVault.sol

2.6.1

	line 122

	"token" should be "tokens"

	"of" should be "for"
	
Modified Comment:

	/// @notice function to allow users to burn LP tokens in exchange for ETH or dETH

2.6.2

	line 227

	"determines" is misspelled as "determins"
	
Modified Comment:

	/// @notice Utility function that determines whether an LP can be burned for dETH if the associated derivatives have been minted

2.7

Contract: GiantMevAndFeesPool.sol

2.7.1

	line 23

	"get" should be "gets"
	
Modified Comment:

	/// @dev Uses contract balance for funding and gets Staking Funds Vault LP in exchange for ETH

2.8

Contract: StakingFundsVault.sol

2.8.1

	line 87

	"user" should be "user's"
	
Modified Comment:

	// Give anything owed to the user before making updates to user's state

2.8.2

	lines 216, and 217

	"free floating" should be hyphenated as "free-floating"
	
Modified Comments:

	// Withdraw any ETH accrued on free-floating SLOT from syndicate to this contract

	// If a partial list of BLS keys that have free-floating staked are supplied, then partial funds accrued will be fetched

2.8.3

	line 235

	"purpose" should be "purposes"
	
Modified Comment:

	/// @notice function to allow admins to withdraw ETH from the vault for staking purposes

2.8.4

	line 336

	"after transfer" should be hyphenated as "after-transfer"
	
Modified Comment:

	// in case the new user has existing rewards - give it to them so that the after-transfer hook does not wipe pending rewards

2.8.5

	line 348

	the "that" before "double" is unnecessary
	
Modified Comment:

	// claim is calculated on full balance not amount being transferred so double claims are not possible

2.9

Contract: Syndicate.sol

2.9.1

	lines 77, 98, 372, 377, 617, 639, and 663

	"free floating" should be hyphenated as "free-floating"
	
Modified Comments:

	/// @notice Last cached highest seen balance for all free-floating shares

	/// @notice Total amount of free-floating sETH staked

	/// @notice Using `highestSeenBalance`, this is the amount that is separately allocated to either free-floating or collateralized SLOT holders

	// Get the amount of ETH eligible for free-floating sETH or collateralized SLOT stakers

	// For the free-floating and collateralized SLOT of the knot, snapshot the accumulated ETH per share

	/// @dev Business logic for allowing a free-floating SLOT holder to claim their share of ETH

	// Update the total ETH claimed by the free-floating SLOT holder based on their share of sETH

2.9.2

	line 356

	"public" is misspelled as "publice"
	
Modified Comment:

	/// @notice Calculate the amount of unclaimed ETH for a given BLS public key + free floating SLOT staker without factoring in unprocessed rewards

2.9.3

	line 398

	"collateralized" is misspelled as "collatearlized"
	
Modified Comment:

	/// @notice Preview the amount of unclaimed ETH available for a collateralized SLOT staker against a KNOT which factors in unprocessed rewards from new ETH sent to contract

2.9.4

	line 490

	"amongs" should be "among"
	
Modified Comment:

	/// Given an amount of ETH allocated to the collateralized SLOT owners of a KNOT, distribute this among the current set of collateralized owners (a dynamic set of addresses and balances)

2.9.5

	line 496

	"its" should be "it's"
	
Modified Comment:

	// Get information about the knot i.e. associated house and whether it's active

2.9.6

	line 565

	"incoming" is misspelled as "incomming"
	
Modified Comment:

	// incoming knot collateralized SLOT holders do not get historical earnings

2.9.7

	line 609

	the "a" before "specific" is unnecessary
	
Modified Comment:

	/// @dev Business logic for de-registering specific knots assuming all accrued ETH has been processed

2.9.8

	line 623

	"reduces" should be "reduced"

Modified Comment:

	// Total number of registered knots with the syndicate reduced by one

2.9.9

	line 654

	"function" is misspelled as "funtion"
	
Modified Comment:

	// this means that user can call the function even if there is nothing to claim but the

2.10

Contract: LiquidStakingManager.sol

2.10.1

	line 44

	consider changing "appointing" to "appointment" for better readability
	
Modified Comment:

	/// @notice signalize appointment of a representative for a smart wallet by the node runner

2.10.2

	line 86

	"signalize" should be "signalizes"
	
Modified Comment:

	/// @notice signalizes that a new BLS public key for an LSD validator has been registered

2.10.3

	line 101

	"admitting" is misspelled as "admiting"
	
Modified Comment:

	/// @notice address of optional gatekeeper for admitting new knots to the house created by the network

2.10.4

	line 119

	"enables" should be "enabled"
	
Modified Comment:

	/// @notice whitelisting indicator. true for enabled and false for disabled

2.10.5

	line 217

	"de register" should be hyphenated as "de-register"
	
Modified Comment:

	/// @notice For knots no longer operational, DAO can de-register the knot from the syndicate

2.10.6

	line 222

	"of" after "preparation" should be "for"

	"are" after "which" should be "is"
	
Modified Comment:

	/// @notice In preparation for a rage quit, restore sETH to a smart wallet which is recoverable with the execution methods in the event this step does not go to plan

2.10.7

	line 225

	"free floating" should be hyphenated as "free-floating"
	
Modified Comment:

	/// @param _amounts Amounts of free-floating sETH that will be unstaked

2.10.8

	line 275

	"noderunner" should be two words
	
Modified Comment:

	/// @notice function to enable/disable whitelisting of a node runner

2.10.9

	line 476

	"validator" is misspelled as "validtor"

	"initials" is misspelled as "initals"
	
Modified Comment:

	// register validator initials for each of the KNOTs

2.10.10

	line 520

	"backed up" should be hyphenated as "backed-up"
	
Modified Comment:

	/// @param _ciphertexts List of backed-up validator operations encrypted and stored to the Ethereum blockchain

2.10.11

	line 845

	"house keeper"  should be one word
	
Modified Comment:

	// Give liquid staking manager ability to manage keepers and set a housekeeper if decided by the network

2.10.12

	line 858

	"reward sharing" should be hyphenated as "reward-sharing"
	
Modified Comment:

	// Deploy the EIP1559 transaction reward-sharing contract but no priority required because sETH will be auto staked

2.10.13

	lines 903, and 920

	"overridden" is misspelled as "overriden"
	
Modified Comments:

	/// @dev Something that can be overridden during testing

	/// @dev This can be overridden to customise fee percentages

2.11

Contract: ETHPoolLPFactory.sol

2.11.1

	line 12

	consider changing "issuing" to "to issue" for better readability
	
Modified Comment:

	/// @dev For pools accepting ETH for validator staking, this contract will manage to issue LPs for deposits

2.11.2

	line 51

	"of" should be "for"
	
Modified Comment:

	/// @param _newLPTokens Array of new LP tokens to be minted in exchange for old LP tokens

2.11.3

	line 74

	"Instance" is misspelled as "Instane"
	
Modified Comment:

	/// @param _newLPToken Instance of the new LP token (to be minted)

2.11.4

	line 118

	"it's" should be "its"

	"is" should be "are"
	
Modified Comment:

	// KNOT and its LP token are already registered

2.11.5

	lines 124, and 150

	"depositor" is misspelled as "depoistor"
	
Modified Comments:

	// mint LP tokens for the depositor with 1:1 ratio of LP tokens and ETH supplied

	the above comment is the same for both lines

3.

Typo in user message of code in:

Contract: SavETHVault.sol

	line 115

	"Inconsistent" is misspelled as "Inconsisent"
	
Modified Code:

	require(numOfTokens == _amounts.length, "Inconsistent array length");