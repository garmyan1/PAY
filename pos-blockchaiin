import hashlib
import time
import json
import os
from flask import Flask, request, jsonify

class Block:
    def __init__(self, index, previous_hash, timestamp, transactions, validator, signature):
        self.index = index
        self.previous_hash = previous_hash
        self.timestamp = timestamp
        self.transactions = transactions
        self.validator = validator
        self.signature = signature
        self.hash = self.calculate_hash()
    
    def calculate_hash(self):
        block_data = json.dumps({
            "index": self.index,
            "previous_hash": self.previous_hash,
            "timestamp": self.timestamp,
            "transactions": self.transactions,
            "validator": self.validator,
            "signature": self.signature
        }, sort_keys=True)
        return hashlib.sha256(block_data.encode()).hexdigest()

class Blockchain:
    def __init__(self):
        self.chain = []
        self.transactions = []
        self.nodes = set()
        self.create_genesis_block()
    
    def create_genesis_block(self):
        genesis_block = Block(0, "0", time.time(), [], "genesis_validator", "signature")
        self.chain.append(genesis_block)
    
    def add_transaction(self, sender, recipient, amount):
        transaction = {
            "sender": sender,
            "recipient": recipient,
            "amount": amount
        }
        self.transactions.append(transaction)
    
    def add_block(self, validator, signature):
        previous_block = self.chain[-1]
        new_block = Block(
            len(self.chain), previous_block.hash, time.time(), self.transactions, validator, signature
        )
        self.transactions = []
        self.chain.append(new_block)
        return new_block
    
    def get_last_block(self):
        return self.chain[-1]

blockchain = Blockchain()
app = Flask(__name__)

@app.route('/transactions/new', methods=['POST'])
def new_transaction():
    data = request.get_json()
    blockchain.add_transaction(data['sender'], data['recipient'], data['amount'])
    return jsonify({"message": "Transaction added"}), 201

@app.route('/mine', methods=['GET'])
def mine():
    validator = "PoS_Validator_1"  # Placeholder validator
    signature = "signature_here"  # Placeholder signature
    new_block = blockchain.add_block(validator, signature)
    return jsonify({"message": "New Block Added", "block_hash": new_block.hash})

@app.route('/chain', methods=['GET'])
def full_chain():
    response = {
        "chain": [block.__dict__ for block in blockchain.chain]
    }
    return jsonify(response), 200

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
