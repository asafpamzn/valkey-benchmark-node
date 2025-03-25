# Valkey Benchmark Node

Valkey Benchmark Node is a benchmark tool for Valkey. This tool allows developers to conduct performance testing in node.js. It supports various testing scenarios, including throughput testing, latency measurements, and custom command benchmarking.

Key Advantages:

1. **Enhanced node.js Developer Experience** Built with native Valkey clients in node.js, providing authentic end-to-end performance measurements in your production environment.

2. **Advanced Cluster Testing**: Leverages the [valkey-GLIDE](https://github.com/valkey-io/valkey-glide) client to support:
   - Dynamic cluster topology updates
   - Read replica operations
  
3. **Extensible Command Framework**: 
   - Supports custom commands and complex operations
   - Test Lua scripts and multi-key transactions
   - Benchmark your specific use cases and data patterns


## Installation

1. Clone the repository:
    ```bash
    git clone <repository-url>
    cd valkey-benchmark-node
    ```

2. Install dependencies:
    ```bash
    npm install
    ```

## Dependencies

This tool requires the following Node.js packages:
- `@valkey/valkey-glide`: Valkey GLIDE client library
- `yargs`: Command-line argument parsing

Install them using:
```bash
npm install @valkey/valkey-glide yargs
```

## Basic Usage

Run a basic benchmark:
```bash
node valkey-benchmark.js -h localhost -p 6379
```

Common usage patterns:
```bash
# Run SET benchmark with 50 parallel clients
node valkey-benchmark.js -c 50 -t set

# Run GET benchmark with rate limiting
node valkey-benchmark.js -t get --qps 1000

# Run benchmark for specific duration
node valkey-benchmark.js --test-duration 60

# Run benchmark with sequential keys
node valkey-benchmark.js --sequential 1000000
```

## Configuration Options

### Basic Options
- `-h, --host <hostname>`: Server hostname (default: "127.0.0.1")
- `-p, --port <port>`: Server port (default: 6379)
- `-c, --clients <num>`: Number of parallel connections (default: 50)
- `-n, --requests <num>`: Total number of requests (default: 100000)
- `-d, --datasize <bytes>`: Data size for SET operations (default: 3)
- `-t, --type <command>`: Command to benchmark (e.g., SET, GET)

### Advanced Options
- `--threads <num>`: Number of worker threads (default: 1)
- `--test-duration <seconds>`: Run test for specified duration
- `--sequential <keyspace>`: Use sequential keys
- `--random <keyspace>`: Use random keys from keyspace

### Rate Limiting Options
- `--qps <num>`: Limit queries per second
- `--start-qps <num>`: Starting QPS for dynamic rate
- `--end-qps <num>`: Target QPS for dynamic rate
- `--qps-change-interval <seconds>`: Interval for QPS changes
- `--qps-change <num>`: QPS change amount per interval

### Security Options
- `--tls`: Enable TLS connection

### Cluster Options
- `--cluster`: Use cluster client
- `--read-from-replica`: Read from replica nodes

## Test Scenarios

### Throughput Testing
```bash
# Maximum throughput test
node valkey-benchmark.js -c 100 -n 1000000

# Rate-limited test
node valkey-benchmark.js -c 50 --qps 5000
```

### Latency Testing
```bash
# Low-concurrency latency test
node valkey-benchmark.js -c 1 -n 10000

# High-concurrency latency test
node valkey-benchmark.js -c 200 -n 100000
```

### Duration-based Testing
```bash
# Run test for 5 minutes
node valkey-benchmark.js --test-duration 300
```

### Key Space Testing
```bash
# Sequential keys
node valkey-benchmark.js --sequential 1000000

# Random keys
node valkey-benchmark.js -r 1000000
```

## Output and Statistics
The benchmark tool provides real-time and final statistics including:

### Real-time Metrics
- Current throughput (requests/second)
- Overall throughput
- Average latency

### Final Report
- Total execution time
- Total requests completed
- Average throughput
- Latency percentiles (P50, P95, P99)
- Min/Max/Avg latencies

Example output:
```plaintext
Final Results:
=============
Total time: 20.01 seconds
Requests completed: 1365377
Requests per second: 68234.73
Total errors: 0

Latency Statistics (ms):
=====================
Minimum: 0.000
Average: 0.116
Maximum: 11.000
Median (p50): 0.000
95th percentile: 1.000
99th percentile: 1.000

Latency Distribution:
====================
<= 0.1 ms: 88.47% (1208014 requests)
<= 0.5 ms: 0.00% (0 requests)
<= 1.0 ms: 11.50% (157005 requests)
<= 2.0 ms: 0.00% (40 requests)
<= 5.0 ms: 0.01% (174 requests)
<= 10.0 ms: 0.01% (135 requests)
<= 20.0 ms: 0.00% (9 requests)
<= 50.0 ms: 0.00% (0 requests)
<= 100.0 ms: 0.00% (0 requests)
<= 200.0 ms: 0.00% (0 requests)
<= 500.0 ms: 0.00% (0 requests)
<= 1000.0 ms: 0.00% (0 requests)
```

## Custom Commands
To implement custom commands, create a custom command file:

```javascript
// Custom commands implementation
const CustomCommands = {
    async execute(client) {
        try {
            await client.set('custom:key', 'custom:value', {
                conditionalSet: "onlyIfDoesNotExist",
                returnOldValue: true});
            return true;
        } catch (error) {
            console.error('Custom command error:', error);
            return false;
        }
    }
};
```

Run custom command benchmark:
```bash
node valkey-benchmark.js -t custom --custom-command-file custom-commands.js
```

## Contributing
Contributions are welcome! Please feel free to submit a Pull Request.
