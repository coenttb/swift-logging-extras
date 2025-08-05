# swift-logging-extras

Extended functionality for Swift's logging system with dependency injection support.

![Version](https://img.shields.io/badge/version-0.0.1-green.svg)
![Swift](https://img.shields.io/badge/swift-6.0-orange.svg)
![Platforms](https://img.shields.io/badge/platforms-iOS%20%7C%20macOS%20%7C%20tvOS%20%7C%20watchOS-lightgrey.svg)

## Features

* **Dependencies integration**: Built-in support for the Dependencies package for clean dependency injection
* **Test support**: Automatic test logger configuration with process name
* **Enhanced logging**: Additional logging functionality with file and line metadata
* **Type-safe**: Full type safety with Swift's Logger type
* **Retroactive conformance**: Clean integration with existing Logger types

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

## Usage

### Basic Usage with Dependencies

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

### Test Usage

In tests, the logger is automatically configured with the process name:

```swift
import LoggingExtras
import DependenciesTestSupport
import Testing

@Test
func testLogging() async throws {
    try await withDependencies {
        // Logger is automatically set to test value
    } operation: {
        @Dependency(\.logger) var logger
        logger.info("Test message") // Will include process name
    }
}
```

### Enhanced Logging with Metadata

The package includes an enhanced logging method that automatically adds file and line metadata:

```swift
@Dependency(\.logger) var logger

// This will include file and line information in the metadata
logger.log(
    .info,
    "Operation completed",
    metadata: ["userId": "12345"]
)
```

### Custom Logger Configuration

You can override the logger dependency for specific features:

```swift
try await withDependencies {
    $0.logger = Logger(label: "com.example.myapp.feature")
} operation: {
    // Your feature code with custom logger
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

### Logger Extensions

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

Enhanced logging method that includes file and line metadata.

## Requirements

- Swift 6.0+
- iOS 13.0+ / macOS 10.15+ / tvOS 13.0+ / watchOS 6.0+

## License

This package is licensed under the same terms as the swift-environment-variables package.