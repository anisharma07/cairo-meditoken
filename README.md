# MediToken (MED) - Cairo Smart Contract

A comprehensive ERC20 token implementation built on StarkNet with an integrated airdrop mechanism for fair token distribution in the healthcare ecosystem.

Deployed Contract :- [0x07e0b09cc6209d4211f150944e7fc0dab7338f0a3a6199ff96d4667bef0e68bc](https://sepolia.starkscan.co/token/0x07e0b09cc6209d4211f150944e7fc0dab7338f0a3a6199ff96d4667bef0e68bc#read-write-contract-sub-read)

## 🌟 Features

### Core ERC20 Functionality

- **Standard ERC20 Implementation**: Full compliance with ERC20 standard using OpenZeppelin Cairo contracts
- **Token Details**:
  - Name: `Meditoken`
  - Symbol: `MED`
  - Decimals: `18`
  - Precision: `10^18` (1 MED = 1,000,000,000,000,000,000 wei)

### Airdrop System

- **Limited Distribution**: First 20 addresses can claim free tokens
- **Fair Allocation**: Each eligible address receives exactly 25 MED tokens
- **Anti-Gaming Protection**: One claim per address, no double claiming
- **Transparent Tracking**: Public functions to check claim status and remaining slots

## 🚀 Quick Start

### Prerequisites

- [Scarb](https://docs.swmansion.com/scarb/) - Cairo package manager
- [Starknet Foundry](https://foundry-rs.github.io/starknet-foundry/) - Testing framework
- [Starkli](https://github.com/xJonathanLEI/starkli) - StarkNet CLI (for deployment)

### Installation

```bash
# Clone the repository
git clone <your-repo-url>
cd meditoken

# Install dependencies
scarb build
```

### Testing

```bash
# Run all tests
snforge test

# Run specific test category
snforge test test_airdrop  # Airdrop tests
snforge test test_constructor  # Basic ERC20 tests

# Run with verbose output
snforge test -v
```

### Deployment

#### Local/Testnet Deployment

```bash
# 1. Build the contract
scarb build

# 2. Declare the contract (replace with your account details)
starkli declare target/dev/meditoken_MedToken.contract_class.json \
    --account <your-account> \
    --keystore <your-keystore>

# 3. Deploy the contract
# Parameters: initial_tokens (felt252), recipient (ContractAddress)
starkli deploy <class-hash> \
    1000 \
    0x1234567890123456789012345678901234567890123456789012345678901234 \
    --account <your-account> \
    --keystore <your-keystore>
```

#### Mainnet Deployment

**⚠️ Warning**: Thoroughly test on testnets before mainnet deployment!

```bash
# Use --network mainnet flag
starkli declare target/dev/meditoken_MedToken.contract_class.json \
    --network mainnet \
    --account <your-account> \
    --keystore <your-keystore>
```

## 📋 Contract Interface

### Constructor

```cairo
fn constructor(
    initial_tokens: felt252,    // Number of tokens to mint initially
    recipient: ContractAddress  // Address to receive initial tokens
)
```

### ERC20 Functions

Standard ERC20 functions are available:

- `transfer(to: ContractAddress, amount: u256) -> bool`
- `transfer_from(from: ContractAddress, to: ContractAddress, amount: u256) -> bool`
- `approve(spender: ContractAddress, amount: u256) -> bool`
- `balance_of(account: ContractAddress) -> u256`
- `allowance(owner: ContractAddress, spender: ContractAddress) -> u256`
- `total_supply() -> u256`
- `name() -> ByteArray`
- `symbol() -> ByteArray`
- `decimals() -> u8`

### Airdrop Functions

#### `claim_airdrop()`

Allows eligible addresses to claim their free 25 MED tokens.

**Requirements:**

- Address must not have claimed before
- Airdrop limit (20 recipients) must not be reached

**Events:** Emits `AirdropClaimed` event

#### `has_claimed_airdrop(address: ContractAddress) -> bool`

Checks if a specific address has already claimed their airdrop.

#### `get_airdrop_count() -> u32`

Returns the total number of addresses that have claimed airdrops.

#### `get_remaining_airdrops() -> u32`

Returns the number of airdrop slots remaining (20 - claimed count).

## 🔒 Security Features

### Airdrop Protection

- **Single Claim Enforcement**: Each address can only claim once
- **Supply Control**: Limited to exactly 20 recipients × 25 tokens = 500 MED total airdrop
- **State Tracking**: Persistent storage tracks all claims

### ERC20 Security

- **OpenZeppelin Base**: Built on battle-tested OpenZeppelin Cairo contracts
- **Overflow Protection**: Built-in SafeMath equivalent protections
- **Standard Compliance**: Full ERC20 standard implementation

## 📊 Token Economics

### Initial Distribution

- **Genesis Supply**: Configurable at deployment (set by `initial_tokens` parameter)
- **Airdrop Pool**: 500 MED tokens (20 recipients × 25 MED each)
- **Total Max Supply**: Genesis Supply + 500 MED

### Airdrop Mechanics

- **Eligibility**: First-come, first-served basis
- **Amount**: Fixed 25 MED tokens per eligible address
- **Limit**: Maximum 20 recipients
- **Claiming**: Anyone can call `claim_airdrop()` until limit reached

## 🧪 Testing Coverage

The contract includes comprehensive tests covering:

### ERC20 Functionality

- ✅ Constructor initialization
- ✅ Token transfers (standard and edge cases)
- ✅ Approval and allowance mechanisms
- ✅ Transfer from functionality
- ✅ Balance and supply tracking
- ✅ Decimal calculations

### Airdrop System

- ✅ Successful airdrop claims
- ✅ Double-claim prevention
- ✅ Multiple user claims
- ✅ Limit enforcement
- ✅ State tracking accuracy
- ✅ Integration with ERC20 functions

### Edge Cases

- ✅ Zero transfers
- ✅ Self transfers
- ✅ Insufficient balance scenarios
- ✅ Insufficient allowance scenarios
- ✅ Multiple approvals

## 📈 Usage Examples

### Deploying the Contract

```cairo
// Deploy with 1000 initial tokens to a specific recipient
let initial_tokens: felt252 = 1000;
let recipient: ContractAddress = 0x123...;

// This creates 1000 MED tokens (1000 × 10^18 wei)
```

### Claiming Airdrop

```cairo
// Any address can attempt to claim
contract.claim_airdrop();

// Check if successful
let has_claimed = contract.has_claimed_airdrop(my_address);
let remaining_slots = contract.get_remaining_airdrops();
```

### Using as Standard ERC20

```cairo
// Transfer tokens
contract.transfer(recipient, 100 * 10^18);  // Transfer 100 MED

// Approve spending
contract.approve(spender, 50 * 10^18);  // Approve 50 MED

// Check balance
let balance = contract.balance_of(user_address);
```

## 🏗️ Project Structure

```
meditoken/
├── src/
│   ├── lib.cairo              # Library entry point
│   └── contracts/
│       └── meditoken.cairo    # Main contract implementation
├── tests/
│   └── test_contract.cairo    # Comprehensive test suite
├── Scarb.toml                 # Project configuration
├── snfoundry.toml            # Foundry configuration
└── README.md                 # This file
```

## 🔧 Development

### Building

```bash
scarb build
```

### Testing

```bash
# Run all tests
snforge test

# Run with verbose output
snforge test -v

# Run specific test
snforge test test_airdrop_claim_successful
```

### Code Formatting

```bash
scarb fmt
```

## 📝 Events

### AirdropClaimed

```cairo
struct AirdropClaimed {
    recipient: ContractAddress,  // Address that claimed
    amount: u256,               // Amount claimed (always 25 MED)
}
```

Emitted when someone successfully claims their airdrop tokens.

## ⚡ Gas Optimization

The contract is optimized for gas efficiency:

- **Storage Layout**: Efficient storage structure using Cairo's native `Map` and simple types
- **Batch Operations**: Single storage writes where possible
- **Component Reuse**: Leverages OpenZeppelin's optimized ERC20 implementation

## 🤝 Contributing

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## ⚠️ Disclaimer

This contract is for educational and development purposes. Ensure thorough testing and auditing before any mainnet deployment.

## 🔗 Links

- [Cairo Documentation](https://book.cairo-lang.org/)
- [StarkNet Documentation](https://docs.starknet.io/)
- [OpenZeppelin Cairo Contracts](https://github.com/OpenZeppelin/cairo-contracts)
- [Starknet Foundry](https://foundry-rs.github.io/starknet-foundry/)

---

**Built with ❤️ for the Healthcare ecosystem**
