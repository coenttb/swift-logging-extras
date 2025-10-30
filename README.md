# swift-logging-extras

[![CI](https://github.com/coenttb/swift-logging-extras/workflows/CI/badge.svg)](https://github.com/coenttb/swift-logging-extras/actions/workflows/ci.yml)
![Development Status](https://img.shields.io/badge/status-active--development-blue.svg)

Extensions for Swift's Logging framework with dependency injection support.

## Overview

This package extends Apple's swift-log with integration for Point-Free's Dependencies library, providing dependency injection support for Logger instances. It includes automatic test logger configuration and enhanced logging methods with metadata support.

## Features

- Dependencies integration for Logger instances
- Automatic test logger configuration with process name
- Enhanced logging method with file and line metadata
- TestDependencyKey conformance for Logger
- Type-safe dependency access via @Dependency property wrapper

## Installation

### Swift Package Manager

Add the following to your `Package.swift` file:

```swift
dependencies: [
    .package(url: "https://github.com/coenttb/swift-logging-extras", from: "0.0.1")
]
```

Then add `LoggingExtras` to your target dependencies:

```swift
targets: [
    .target(
        name: "YourTarget",
        dependencies: [
            .product(name: "LoggingExtras", package: "swift-logging-extras")
        ]
    )
]
```

## Quick Start

```swift
import LoggingExtras
import Dependencies

struct MyFeature {
    @Dependency(\.logger) var logger

    func doSomething() {
        logger.info("Starting operation")
        // ... your code ...
        logger.debug("Operation completed")
    }
}
```

## Usage

### Basic Usage with Dependencies

Access the logger through the Dependencies system:

```swift
import LoggingExtras
import Dependencies

struct MyService {
    @Dependency(\.logger) var logger

    func performTask() {
        logger.info("Task started")
        logger.debug("Processing...")
        logger.notice("Task completed")
    }
}
```

### Test Usage

In tests, the logger is automatically configured with the process name:

```swift
import LoggingExtras
import DependenciesTestSupport
import Testing

@Test
func testLogging() async throws {
    try await withDependencies { _ in
        // Logger is automatically set to test value
    } operation: {
        @Dependency(\.logger) var logger
        logger.info("Test message") // Will include process name
    }
}
```

### Custom Logger Configuration

Override the logger dependency for specific features:

```swift
import LoggingExtras
import Dependencies
import DependenciesTestSupport

@Test
func testWithCustomLogger() async throws {
    try await withDependencies {
        $0.logger = Logger(label: "com.example.myapp.feature")
    } operation: {
        @Dependency(\.logger) var logger
        logger.info("Using custom logger")
    }
}
```

### Enhanced Logging with Metadata

Use the enhanced logging method that automatically adds file and line metadata:

```swift
import LoggingExtras
import Dependencies
import Logging

struct MyFeature {
    @Dependency(\.logger) var logger

    func processUser(id: String) {
        // Automatically includes file and line information in metadata
        logger.log(
            .info,
            "Processing user",
            metadata: ["userId": "\(id)"]
        )
    }
}
```

## API Reference

### DependencyValues Extension

```swift
extension DependencyValues {
    public var logger: Logger { get set }
}
```

Access the logger through the Dependencies system.

### Logger Extension

```swift
extension Logger: TestDependencyKey {
    public static let testValue = Logger(label: ProcessInfo.processInfo.processName)
}
```

Provides automatic test logger configuration.

### Enhanced Logging Method

```swift
extension Logger {
    public func log(
        _ level: Logger.Level,
        _ message: @autoclosure () -> Logger.Message,
        metadata: Logger.Metadata? = nil,
        file: String = #file,
        function: String = #function,
        line: UInt = #line
    )
}
```

Enhanced logging method that merges provided metadata with file and line information.

## Related Packages

- [swift-dependencies](https://github.com/pointfreeco/swift-dependencies) - A dependency management library inspired by SwiftUI's Environment
- [swift-log](https://github.com/apple/swift-log) - A Logging API for Swift

## Requirements

- Swift 6.0+
- iOS 13.0+ / macOS 10.15+ / tvOS 13.0+ / watchOS 6.0+

## License

This package is released under the Apache 2.0 license. See [LICENSE](LICENSE) for details.

## Contributing

Contributions are welcome. Please open an issue or pull request on GitHub.
