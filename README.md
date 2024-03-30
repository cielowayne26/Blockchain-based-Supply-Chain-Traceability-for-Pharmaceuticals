# Blockchain-based-Supply-Chain-Traceability-for-Pharmaceuticals
Use blockchain technology to create a secure and transparent system for tracking pharmaceuticals from manufacturer to patient, combating counterfeit drugs.
import hashlib
import json
from time import time

class Blockchain:
    def __init__(self):
        self.chain = []
        self.current_transactions = []
        # Create the genesis block
        self.new_block(previous_hash='1', proof=100)

    def new_block(self, proof, previous_hash=None):
        # Creates a new block and adds it to the chain
        block = {
            'index': len(self.chain) + 1,
            'timestamp': time(),
            'transactions': self.current_transactions,
            'proof': proof,
            'previous_hash': previous_hash or self.hash(self.chain[-1]),
        }
        self.current_transactions = []
        self.chain.append(block)
        return block

    def new_transaction(self, sender, recipient, product):
        # Adds a new transaction to the list of transactions
        self.current_transactions.append({
            'sender': sender,
            'recipient': recipient,
            'product': product,
        })
        return self.last_block['index'] + 1

    @staticmethod
    def hash(block):
        # Hashes a block
        block_string = json.dumps(block, sort_keys=True).encode()
        return hashlib.sha256(block_string).hexdigest()

    @property
    def last_block(self):
        return self.chain[-1]

    def proof_of_work(self, last_proof):
        proof = 0
        while self.valid_proof(last_proof, proof) is False:
            proof += 1
        return proof

    @staticmethod
    def valid_proof(last_proof, proof):
        guess = f'{last_proof}{proof}'.encode()
        guess_hash = hashlib.sha256(guess).hexdigest()
        return guess_hash[:4] == "0000"

# Example usage
blockchain = Blockchain()
blockchain.new_transaction(sender="Pharma A", recipient="Distributor B", product="Drug X")
blockchain.new_transaction(sender="Distributor B", recipient="Pharmacy C", product="Drug X")
proof = blockchain.proof_of_work(blockchain.last_block['proof'])
previous_hash = blockchain.hash(blockchain.last_block)
blockchain.new_block(proof, previous_hash)

# Print the blockchain
for block in blockchain.chain:
    print(json.dumps(block, indent=4))
