# BANK TRANSACTION PROCESSING SYSTEM
### Student Information: ainaafeeya
### Platform: Ubuntu
### Date: April 21, 2026


# 1. Problem Statement
Banking systems process thousands of financial transactions daily, including deposits, withdrawals, and transfers. Processing these transactions efficiently is crucial for system performance. However, it is unclear whether using concurrent (threading) or parallel (multiprocessing) techniques provides better performance compared to traditional sequential processing. This project aims to compare these three approaches by processing 100,000 random bank transactions and measuring their execution times to determine which method is most efficient.
# 2. Objectives
To implement three transaction processing methods:
* Sequential processing (single-threaded)
* Concurrent processing using ThreadPoolExecutor
* Parallel processing using multiprocessing.Pool<br>


To generate 100,000 random transactions with three types: deposit, withdraw, and transfer

To measure and record the execution time for each processing method

To calculate the speedup factor of concurrent and parallel methods compared to sequential

To analyze which method performs best for this transaction processing workload
# 3. Implementation
## 3.1 Source Code 
import random
import time
from concurrent.futures 
import ThreadPoolExecutor
from multiprocessing import Pool

def generate_transactions(n):
    """Generate random bank transactions"""
    transactions = []
    for _ in range(n):
        action = random.choice(["deposit", "withdraw", "transfer"])
        amount = random.randint(1, 1000)
        transactions.append((action, amount))
    return transactions

def process_chunk(chunk):
    """Process a chunk of transactions"""
    balance = 0
    deposit = withdraw = transfer = 0
    
    for action, amount in chunk:
        if action == "deposit":
            balance += amount
            deposit += 1
        elif action == "withdraw":
            balance -= amount
            withdraw += 1
        else:
            balance -= amount
            transfer += 1
    
    return balance, deposit, withdraw, transfer

def sequential(transactions):
    """Process sequentially"""
    start = time.time()
    result = process_chunk(transactions)
    elapsed = time.time() - start
    return result, elapsed

def concurrent(transactions):
    """Process with threading"""
    start = time.time()
    
    num_workers = 4
    chunk_size = len(transactions) // num_workers
    chunks = [transactions[i:i+chunk_size] for i in range(0, len(transactions), chunk_size)]
    
    with ThreadPoolExecutor(max_workers=num_workers) as executor:
        results = executor.map(process_chunk, chunks)
    
    elapsed = time.time() - start
    return results, elapsed

def parallel(transactions):
    """Process with multiprocessing"""
    start = time.time()
    
    num_workers = 4
    chunk_size = len(transactions) // num_workers
    chunks = [transactions[i:i+chunk_size] for i in range(0, len(transactions), chunk_size)]
    
    with Pool(num_workers) as pool:
        results = pool.map(process_chunk, chunks)
    
    elapsed = time.time() - start
    return results, elapsed

if __name__ == "__main__":
    print("Bank Transaction Processing System\n")
    
    # Generate 100,000 transactions
    n = 100000
    print(f"Processing {n:,} transactions...\n")
    transactions = generate_transactions(n)
'''
    
    
  ## 3.2 Implementation Description
  | Function                   | Purpose                                                           |
|--------------------------|-------------------------------------------------------------------|
| generate_transactions(n) | Creates random transactions with random amounts                   |
| process_chunk(chunk)     | Processes a batch and returns balance and counts                  |
| sequential(transactions) | Processes all transactions in one thread                          |
| concurrent(transactions) | Uses 4 threads to process 4 chunks                                |
| parallel(transactions)   | Uses 4 processes to process 4 chunks                               |

# 4. Output Results
<img width="479" height="286" alt="Screenshot 2026-04-21 160042" src="https://github.com/user-attachments/assets/9b6f1449-d30f-4df2-80c2-734f788e0c14" />

# 5. Performance Analysis
## 5.1 Results Summary
| Processing Method        | Time (seconds) | Speedup |
|--------------------------|----------------|---------|
| Sequential               | 0.0147         | 1.00x   |
| Concurrent (4 threads)   | 0.0186         | 0.79x   |
| Parallel (4 processes)   | 0.0417         | 0.35x   |

---

## 5.2 Transaction Statistics

| Transaction Type | Count   |
|------------------|---------|
| Deposits         | 33,340  |
| Withdrawals      | 33,128  |
| Transfers        | 33,532  |
| **Total**        | **100,000** |

## 5.3 Analysis
* Sequential (0.0147s): Fastest method. No overhead from thread or process creation.

* Concurrent (0.0186s): 0.79x slower than sequential. Thread management overhead reduced performance.

* Parallel (0.0417s): 0.35x slower than sequential. Process creation overhead was significant.

# 6. Conclusion
For processing 100,000 bank transactions, sequential processing was the fastest method. Concurrent threading was 21% slower, and parallel multiprocessing was 65% slower than sequential. The overhead of creating and managing threads and processes outweighed any potential parallelization benefits for this workload size.
