# Mini-Wallet Backend Service

## Overview

A production-grade RESTful API for secure wallet management and atomic money transfers. Built with Node.js/Express and PostgreSQL to demonstrate backend engineering best practices.

**Key Focus Areas:**
- Database transactions for atomicity
- RESTful API design with proper HTTP status codes
- Comprehensive validation and error handling
- Production-ready code structure

## Tech Stack

- **Node.js** - Runtime
- **Express.js** - Web framework
- **PostgreSQL** - Database with ACID guarantees
- **Jest** - Integration testing

## Core Features

### 1. Create Account
Initialize a wallet with initial balance
```
POST /api/account
{"username": "alice", "initial_balance": 100}
```

### 2. Check Balance  
Retrieve current balance for a user
```
GET /api/balance/:user_id
```

### 3. Transfer Money
Atomic transfer between accounts (both debit+credit succeed or both fail)
```
POST /api/transfer
{"from_user_id": 1, "to_user_id": 2, "amount": 50}
```

## Setup Instructions

### Prerequisites
- Node.js v14+
- PostgreSQL v12+

### Installation
```bash
# Clone the repo
git clone https://github.com/nvishnu-vardhan/mini-wallet-backend.git
cd mini-wallet-backend

# Install dependencies
npm install

# Create database
creatdb mini_wallet

# Run migrations
psql -d mini_wallet -f migrations/init.sql

# Configure .env
echo "DATABASE_URL=postgresql://user:pass@localhost:5432/mini_wallet" > .env

# Start server
npm run dev
```

Server runs on http://localhost:3000

## Testing

```bash
# Run integration tests
npm test
```

## Project Structure

```
src/
  app.js              - Express setup
  db.js               - PostgreSQL connection
  routes/
    wallet.js         - All endpoints
  middleware/
    errorHandler.js   - Global error handler
migrations/
  init.sql            - Database schema
tests/
  wallet.test.js      - Integration tests
server.js             - Entry point
```

## How It Works

### Atomic Transfers
The transfer endpoint uses database transactions to ensure atomicity:
1. Begin transaction
2. Check sender has sufficient balance
3. Debit sender account
4. Credit receiver account
5. Commit or rollback

If any step fails, the entire transaction rolls back. **No partial transfers.**

### Data Integrity
- DECIMAL(15,2) for precise money calculations
- CHECK constraints prevent negative balances
- FOREIGN KEY constraints maintain referential integrity

### Error Handling
Clear, actionable error codes:
- `400 INSUFFICIENT_BALANCE` - Not enough funds
- `404 USER_NOT_FOUND` - User doesn't exist  
- `400 INVALID_AMOUNT` - Amount must be positive
- `400 DUPLICATE_USER` - Username already taken

## Example Flow

```
1. Create Alice account with $100
   POST /api/account
   {"username": "alice", "initial_balance": 100}
   → {"user_id": 1, "balance": 100}

2. Create Bob account with $50
   POST /api/account
   {"username": "bob", "initial_balance": 50}
   → {"user_id": 2, "balance": 50}

3. Alice sends $30 to Bob
   POST /api/transfer
   {"from_user_id": 1, "to_user_id": 2, "amount": 30}
   → {"status": "success", "from_balance": 70, "to_balance": 80}

4. Check Alice's balance
   GET /api/balance/1
   → {"user_id": 1, "username": "alice", "balance": 70}
```

## Why This Matters

**ACID Properties**: Demonstrates understanding of database transactions and consistency

**Concurrency Safety**: Row-level locking prevents race conditions

**Production-Ready**: Proper validation, error handling, and testing

**Data Safety**: No negative balances, no lost money, no partial transfers

## Future Enhancements

- JWT authentication
- Rate limiting
- Transaction history API
- Webhook notifications
- Frontend UI
- Docker support
- Load testing

## License

MIT
