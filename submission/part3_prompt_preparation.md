#Part 3: Prompt Preparation
---------------------------

# Selected PR #196 aiokafka
---------------------------


#3.1.1 Repository Context
-------------------------

The aiokafka repository provides an asynchronous Kafka client for Python applications. It allows developers to produce and consume Kafka messages using Python’s `asyncio` framework. The library is designed for high-throughput, non-blocking message processing and is commonly used in microservices, streaming pipelines, and event-driven systems.

The main users of this repository are backend developers who build scalable services that interact with Kafka while using async Python frameworks such as FastAPI or aiohttp. The library abstracts Kafka’s binary protocol and provides a Pythonic interface for producers and consumers.

The problem domain is distributed messaging. Kafka is often used in unreliable network environments where brokers can temporarily fail or become unreachable. aiokafka must handle these situations gracefully without blocking the entire application. Reliability, correct error handling, and predictable async behavior are critical for users running production workloads.



#3.1.2 Pull Request Description
-------------------------------

This pull request improves how the Kafka producer behaves when it cannot fetch metadata from the Kafka cluster. Previously, if metadata requests failed repeatedly, the producer could wait forever, causing message sends to hang without clear feedback to the user.

The PR introduces timeout handling and better error propagation. Now, when metadata cannot be fetched within a reasonable time, the producer stops retrying indefinitely and raises an explicit error. This makes failures visible and prevents applications from getting stuck.

Before this change, the producer appeared alive but silently blocked. After the change, the behavior is predictable: either metadata is fetched successfully, or the application receives a clear failure signal. This is especially important in cloud environments where brokers may restart or network connectivity may be unstable.


#3.1.3 Acceptance Criteria
--------------------------

- When metadata fetch fails repeatedly, the producer should raise a timeout error  
- The producer should not block indefinitely during message send  
- Existing successful metadata fetch behavior should remain unchanged  
- Errors should be logged clearly  
- Unit tests should cover metadata failure scenarios  


#3.1.4 Edge Cases
-----------------

1. Kafka broker temporarily unavailable  
2. Partial metadata response from cluster  
3. Very slow network causing delayed metadata responses  


#3.1.5 Initial Prompt
---------------------

You are working on the aiokafka repository, an asynchronous Kafka client written in Python using asyncio. Your task is to improve the Kafka producer’s behavior when metadata cannot be fetched successfully from the Kafka cluster.

Currently, if metadata requests repeatedly fail, the producer may block indefinitely during message sends. This leads to applications hanging without receiving clear feedback. Your goal is to ensure that metadata fetch failures are handled predictably and transparently.

Implement timeout handling around metadata requests so that the producer does not wait forever. Introduce a retry mechanism with a reasonable limit. If metadata cannot be retrieved within this limit, raise a clear and descriptive exception. Ensure that this error propagates back to the caller instead of being swallowed internally.

The implementation should reuse existing timeout utilities where possible and follow the async programming patterns already used in the codebase. Avoid introducing blocking calls. Ensure that successful metadata fetch behavior remains unchanged.

Acceptance criteria must be met:
- The producer must not block indefinitely
- Errors must be surfaced clearly
- Existing functionality must not regress

Consider edge cases such as slow networks, temporary broker outages, and partial metadata responses. Add or update unit tests to validate failure scenarios and confirm that successful paths still work correctly.
