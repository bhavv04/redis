# My C++ Redis Server

A lightweight Redis-compatible in-memory data store written in C++. Supports strings, lists, and hashes with full Redis Serialization Protocol (RESP) parsing, multi-client concurrency, and periodic disk persistence.

## Features

- **Redis-Compatible Protocol**: Full RESP parsing support
- **Multi-Client Concurrency**: Each client handled in separate threads
- **Data Types**: Strings, Lists, and Hashes
- **Persistence**: Automatic disk dumps every 5 minutes and on shutdown
- **TTL Support**: Key expiration with lazy eviction
- **Thread-Safe**: Mutex-protected data stores

## Quick Start

### Prerequisites

- C++17 (or later) compiler
- POSIX-compatible system (Linux, macOS)
- Make (optional)

### Build

```bash
# Using Makefile (recommended)
make

# Or compile manually
g++ -std=c++17 -pthread -Iinclude src/*.cpp -o my_redis_server
```

### Run

```bash
# Default port (6379)
./my_redis_server

# Custom port
./my_redis_server 6380
```

### Connect

```bash
# Using redis-cli
redis-cli -p 6379

# Example commands
127.0.0.1:6379> PING
PONG
127.0.0.1:6379> SET mykey "Hello World"
OK
127.0.0.1:6379> GET mykey
"Hello World"
```

## Supported Commands

### General Commands
- `PING` - Test server connectivity
- `ECHO <message>` - Echo a message
- `FLUSHALL` - Clear all data

### String Operations
- `SET <key> <value>` - Store a string value
- `GET <key>` - Retrieve a string value
- `DEL <key>` / `UNLINK <key>` - Delete a key
- `KEYS *` - List all keys
- `TYPE <key>` - Get the data type of a key
- `EXPIRE <key> <seconds>` - Set key expiration
- `RENAME <oldkey> <newkey>` - Rename a key

### List Operations
- `LGET <key>` - Get all elements from a list
- `LLEN <key>` - Get list length
- `LPUSH <key> <value1> [value2 ...]` - Push to list head
- `RPUSH <key> <value1> [value2 ...]` - Push to list tail
- `LPOP <key>` - Pop from list head
- `RPOP <key>` - Pop from list tail
- `LINDEX <key> <index>` - Get element at index
- `LSET <key> <index> <value>` - Set element at index
- `LREM <key> <count> <value>` - Remove elements

### Hash Operations
- `HSET <key> <field> <value>` - Set hash field
- `HGET <key> <field>` - Get hash field value
- `HEXISTS <key> <field>` - Check if field exists
- `HDEL <key> <field>` - Delete hash field
- `HLEN <key>` - Get number of fields
- `HKEYS <key>` - Get all field names
- `HVALS <key>` - Get all field values
- `HGETALL <key>` - Get all field-value pairs
- `HMSET <key> <field1> <value1> [field2 value2 ...]` - Set multiple fields

## Architecture

### Core Components

- **RedisServer**: TCP socket handling and client management
- **RedisDatabase**: Thread-safe data storage (singleton pattern)
- **RedisCommandHandler**: RESP protocol parsing and command execution

### Data Storage

- **String Store**: `unordered_map<string, string>`
- **List Store**: `unordered_map<string, vector<string>>`
- **Hash Store**: `unordered_map<string, unordered_map<string, string>>`
- **Expiry Map**: TTL tracking for automatic key expiration

### Concurrency Model

- One thread per client connection
- Single global mutex (`db_mutex`) protecting all data stores
- Lazy expiration on key access

## Persistence

The server automatically saves data to `dump.my_rdb`:
- Every 300 seconds (5 minutes) via background thread
- On graceful shutdown (Ctrl+C)
- Loads existing dump file on startup

## Project Structure

```
├── include/                     # Header files
│   ├── RedisCommandHandler.h
│   ├── RedisDatabase.h
│   └── RedisServer.h
├── src/                         # Implementation files
│   ├── RedisCommandHandler.cpp
│   ├── RedisDatabase.cpp
│   ├── RedisServer.cpp
│   └── main.cpp
├── Makefile                     # Build configuration
├── README.md                    # This file
├── Concepts,UseCases&Tests.md   # Design documentation
└── test_all.sh                  # Test script
```

## Development

### Building from Source

1. Clone the repository
2. Navigate to project directory
3. Run `make` to build
4. Run `make clean` to clean build artifacts

### Testing

Run the provided test script:
```bash
./test_all.sh
```

Or test manually with `redis-cli` or any RESP-compatible client.

## Configuration

- **Default Port**: 6379
- **Persistence File**: `dump.my_rdb`
- **Auto-save Interval**: 300 seconds
- **Protocol**: Redis RESP (REdis Serialization Protocol)

## Limitations

- Single-threaded command execution (per client)
- Text-based persistence format (simplified RDB)
- Memory-only storage (no disk-based datasets)
- Subset of Redis commands implemented


## License

This project is for educational purposes. See the video series for detailed explanations of concepts and implementation decisions.

**Server Name**: `my_redis_server`  
**Default Port**: `6379`  
**Protocol**: Redis RESP