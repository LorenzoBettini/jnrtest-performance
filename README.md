# JnrTest Performance

Performance results for JnrTest compared to JUnit 5.

JnrTest source code is available at: <https://github.com/LorenzoBettini/jnrtest>

To run the performance tests, clone the repository and run:

```sh
./mvnw verify -Pbenchmark
```

## How we measured performance

Two performance statistics were conducted to compare JnrTest with JUnit 5.
We performed two types of measurements.

### Running the same test case in test classes with increasing number of test cases

The first measurement relies on running the same test case multiple times, both in a JUnit 5 and a JnrTest test.

The test case calls these two assertions:

```java
package com.example.testutils;

import static org.assertj.core.api.Assertions.assertThat;

import java.util.List;

public class CommonTestUtils {

	private static List<String> generatedStrings = List.of(
			"Lorem ipsum dolor sit amet, consectetur adipiscing elit.",
			"Sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.",
			"Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.",
			"Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.",
			"Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.",
			"Curabitur pretium tincidunt lacus. Nulla gravida orci a odio.",
			"Nullam varius, turpis et commodo pharetra, est eros bibendum elit, nec luctus magna felis sollicitudin mauris.",
			"Integer in mauris eu nibh euismod gravida",
			"Duis ac tellus et risus vulputate vehicula.",
			"Donec lobortis risus a elit.",
			"Etiam tempor. Ut ullamcorper, ligula eu tempor congue, eros est euismod turpis, id tincidunt sapien risus a quam.",
			"Maecenas fermentum consequat mi. Donec fermentum.",
			"Pellentesque malesuada nulla a mi. Duis sapien sem, aliquet nec, commodo eget",
			"consequat quis, neque. Aliquam faucibus, elit ut dictum aliquet, felis nisl adipiscing sapien, sed malesuada diam lacus eget erat.",
			"Cras mollis scelerisque nunc. Nullam arcu.",
			"Aliquam consequat. Curabitur augue lorem, dapibus quis, laoreet et, pretium ac, nisi.",
			"Aenean magna nisl, mollis quis, molestie eu, feugiat in, orci.",
			"In hac habitasse platea dictumst.",
			"findme!"
	);

	public static void assertStringIsPresent(String str) {
		assertThat(generatedStrings).anyMatch(s -> s.contains(str));
	}

	public static void assertStringIsAbsent(String str) {
		assertThat(generatedStrings).noneMatch(s -> s.contains(str));
	}
}
```

We generate the source code of JUnit 5 and JnrTest test classes that call the same test case body multiple times: 1 (for warm-up), 10, 100, 1000, 5000 (beyond 5000, the Java compiler refuses to compile the test class, as its source is too big).

For JUnit 5, we also generate test classes with the option `@TestInstance(TestInstance.Lifecycle.PER_CLASS)`, to avoid the overhead of creating a new instance of the test class for each test method.

Then, we measure the time taken to run each generated test class with JUnit 5 and JnrTest.

### Running the JnrTest test suite with JUnit 5 and JnrTest runners

The second measurement relies on running the JnrTest test suite implemented in JUnit 5.
Then, by using our implemented translator, we generate the JnrTest versions of the same test classes, and we run them with the JnrTest runner.

The executed test case code is thus the same in both cases, and we can measure the time taken by JUnit 5 and JnrTest to run the same test suite.

We also tried to add the option `@TestInstance(TestInstance.Lifecycle.PER_CLASS)` to the JUnit 5 test classes, but this does not change significantly the execution time, so we do not report those results here.

## Results

We ran the benchmarks on several environments.

- Dell Pro Max Tower T2, with 24 × Intel Core Ultra 9 285K, 32 GB RAM, running Linux.
- Dell XPS 13 laptop, 8 × Intel® Core™ i7-8550U CPU @ 1.80GHz, 16 GB RAM, running Linux.
- On GitHub Actions, in 3 environments: Ubuntu-latest, Windows-latest, MacOS-latest.

The results always first show the JUnit 5 measurement, then the JnrTest measurement, in the same text block.

For the second measurement, the test classes executed by JUnit 5 and JnrTest might differ in the order of execution, but the names of the executed test can be easily matched.

### Dell Pro Max Tower T2

Dell Pro Max Tower T2, with 24 × Intel Core Ultra 9 285K, 32 GB RAM.
Modern (2025) and powerful hardware PC.

First measurement results for JUnit 5:

```text
[INFO] Running com.example.demos.junit.MyJUnit10Spec
[INFO] Tests run: 10, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.010 s -- in com.example.demos.junit.MyJUnit10Spec
[INFO] Running com.example.demos.junit.MyJUnit100Spec
[INFO] Tests run: 100, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.030 s -- in com.example.demos.junit.MyJUnit100Spec
[INFO] Running com.example.demos.junit.MyJUnit1000Spec
[INFO] Tests run: 1000, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.112 s -- in com.example.demos.junit.MyJUnit1000Spec
[INFO] Running com.example.demos.junit.MyJUnit5000Spec
[INFO] Tests run: 5000, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.238 s -- in com.example.demos.junit.MyJUnit5000Spec
```

First measurement results for JUnit 5 with `@TestInstance(TestInstance.Lifecycle.PER_CLASS)`:

```text
[INFO] Running com.example.demos.junit.MyJUnit10OneInstancePerClassSpec
[INFO] Tests run: 10, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.009 s -- in com.example.demos.junit.MyJUnit10OneInstancePerClassSpec
[INFO] Running com.example.demos.junit.MyJUnit100OneInstancePerClassSpec
[INFO] Tests run: 100, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.028 s -- in com.example.demos.junit.MyJUnit100OneInstancePerClassSpec
[INFO] Running com.example.demos.junit.MyJUnit1000OneInstancePerClassSpec
[INFO] Tests run: 1000, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.099 s -- in com.example.demos.junit.MyJUnit1000OneInstancePerClassSpec
[INFO] Running com.example.demos.junit.MyJUnit5000OneInstancePerClassSpec
[INFO] Tests run: 5000, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.225 s -- in com.example.demos.junit.MyJUnit5000OneInstancePerClassSpec
```

First measurement results for JnrTest:

```text
[  START] MyJnr10
Tests run: 10, Succeeded: 10, Failures: 0, Errors: 0 - Time elapsed: 0.001000 s
[  START] MyJnr100
Tests run: 100, Succeeded: 100, Failures: 0, Errors: 0 - Time elapsed: 0.002000 s
[  START] MyJnr1000
Tests run: 1000, Succeeded: 1000, Failures: 0, Errors: 0 - Time elapsed: 0.003000 s
[  START] MyJnr5000
Tests run: 5000, Succeeded: 5000, Failures: 0, Errors: 0 - Time elapsed: 0.010000 s
```

Second measurement results for JUnit 5:

```text
[INFO] Running io.github.lorenzobettini.jnrtest.core.JnrTestConsoleExecutorTest
[INFO] Tests run: 15, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.237 s -- in io.github.lorenzobettini.jnrtest.core.JnrTestConsoleExecutorTest
[INFO] Running io.github.lorenzobettini.jnrtest.core.JnrTestConsoleParallelExecutorTest
[INFO] Tests run: 9, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.064 s -- in io.github.lorenzobettini.jnrtest.core.JnrTestConsoleParallelExecutorTest
[INFO] Running io.github.lorenzobettini.jnrtest.core.JnrTestConsoleReporterTest
[INFO] Tests run: 13, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.110 s -- in io.github.lorenzobettini.jnrtest.core.JnrTestConsoleReporterTest
[INFO] Running io.github.lorenzobettini.jnrtest.core.JnrTestFilterTest
[INFO] Tests run: 6, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.022 s -- in io.github.lorenzobettini.jnrtest.core.JnrTestFilterTest
[INFO] Running io.github.lorenzobettini.jnrtest.core.JnrTestFiltersTest
[INFO] Tests run: 17, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.008 s -- in io.github.lorenzobettini.jnrtest.core.JnrTestFiltersTest
[INFO] Running io.github.lorenzobettini.jnrtest.core.JnrTestParallelRunnerTest
[INFO] Tests run: 1, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.014 s -- in io.github.lorenzobettini.jnrtest.core.JnrTestParallelRunnerTest
[INFO] Running io.github.lorenzobettini.jnrtest.core.JnrTestRunnerTest
[INFO] Tests run: 12, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.059 s -- in io.github.lorenzobettini.jnrtest.core.JnrTestRunnerTest
[INFO] Running io.github.lorenzobettini.jnrtest.core.JnrTestThreadSafeConsoleReporterTest
[INFO] Tests run: 5, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.029 s -- in io.github.lorenzobettini.jnrtest.core.JnrTestThreadSafeConsoleReporterTest
[INFO] Running io.github.lorenzobettini.jnrtest.core.JnrTestThreadSafeRecorderTest
[INFO] Tests run: 8, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.080 s -- in io.github.lorenzobettini.jnrtest.core.JnrTestThreadSafeRecorderTest
[INFO] Running io.github.lorenzobettini.jnrtest.core.JnrTestRecorderTest
[INFO] Tests run: 7, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.054 s -- in io.github.lorenzobettini.jnrtest.core.JnrTestRecorderTest
[INFO] Running io.github.lorenzobettini.jnrtest.core.JnrTestReporterInterfaceTest
[INFO] Tests run: 4, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.002 s -- in io.github.lorenzobettini.jnrtest.core.JnrTestReporterInterfaceTest
[INFO] Running io.github.lorenzobettini.jnrtest.core.JnrTestStatisticsTest
[INFO] Tests run: 7, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.065 s -- in io.github.lorenzobettini.jnrtest.core.JnrTestStatisticsTest
```

Second measurement results for JnrTest:

```text
[  START] JnrTestConsoleExecutorTest in JnrTest
Tests run: 15, Succeeded: 15, Failures: 0, Errors: 0 - Time elapsed: 0.234000 s
[  START] JnrTestConsoleParallelExecutorTest in JnrTest
Tests run: 9, Succeeded: 9, Failures: 0, Errors: 0 - Time elapsed: 0.056000 s
[  START] JnrTestConsoleReporterTest in JnrTest
Tests run: 13, Succeeded: 13, Failures: 0, Errors: 0 - Time elapsed: 0.098000 s
[  START] JnrTestFilterTest in JnrTest
Tests run: 6, Succeeded: 6, Failures: 0, Errors: 0 - Time elapsed: 0.017000 s
[  START] JnrTestFiltersTest in JnrTest
Tests run: 17, Succeeded: 17, Failures: 0, Errors: 0 - Time elapsed: 0.003000 s
[  START] JnrTestParallelRunnerTest in JnrTest
Tests run: 1, Succeeded: 1, Failures: 0, Errors: 0 - Time elapsed: 0.014000 s
[  START] JnrTestRecorderTest in JnrTest
Tests run: 7, Succeeded: 7, Failures: 0, Errors: 0 - Time elapsed: 0.053000 s
[  START] JnrTestReporterInterfaceTest in JnrTest
Tests run: 4, Succeeded: 4, Failures: 0, Errors: 0 - Time elapsed: 0.001000 s
[  START] JnrTestRunnerTest in JnrTest
Tests run: 12, Succeeded: 12, Failures: 0, Errors: 0 - Time elapsed: 0.044000 s
[  START] JnrTestStatisticsTest in JnrTest
Tests run: 7, Succeeded: 7, Failures: 0, Errors: 0 - Time elapsed: 0.061000 s
[  START] JnrTestThreadSafeConsoleReporterTest in JnrTest
Tests run: 5, Succeeded: 5, Failures: 0, Errors: 0 - Time elapsed: 0.027000 s
[  START] JnrTestThreadSafeRecorderTest in JnrTest
Tests run: 8, Succeeded: 8, Failures: 0, Errors: 0 - Time elapsed: 0.069000 s
```

### Dell XPS 13 laptop

Dell XPS 13 laptop, 8 × Intel® Core™ i7-8550U CPU @ 1.80GHz, 16 GB RAM.
Old (2018) but still decent hardware laptop.

First measurement results for JUnit 5:

```text
[INFO] Running com.example.demos.junit.MyJUnit10Spec
[INFO] Tests run: 10, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.034 s -- in com.example.demos.junit.MyJUnit10Spec
[INFO] Running com.example.demos.junit.MyJUnit100Spec
[INFO] Tests run: 100, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.144 s -- in com.example.demos.junit.MyJUnit100Spec
[INFO] Running com.example.demos.junit.MyJUnit1000Spec
[INFO] Tests run: 1000, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.783 s -- in com.example.demos.junit.MyJUnit1000Spec
[INFO] Running com.example.demos.junit.MyJUnit5000Spec
[INFO] Tests run: 5000, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 1.269 s -- in com.example.demos.junit.MyJUnit5000Spe
```

First measurement results for JUnit 5 with `@TestInstance(TestInstance.Lifecycle.PER_CLASS)`:

```text
[INFO] Running com.example.demos.junit.MyJUnit10OneInstancePerClassSpec
[INFO] Tests run: 10, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.037 s -- in com.example.demos.junit.MyJUnit10OneInstancePerClassSpec
[INFO] Running com.example.demos.junit.MyJUnit100OneInstancePerClassSpec
[INFO] Tests run: 100, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.155 s -- in com.example.demos.junit.MyJUnit100OneInstancePerClassSpec
[INFO] Running com.example.demos.junit.MyJUnit1000OneInstancePerClassSpec
[INFO] Tests run: 1000, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.534 s -- in com.example.demos.junit.MyJUnit1000OneInstancePerClassSpec
[INFO] Running com.example.demos.junit.MyJUnit5000OneInstancePerClassSpec
[INFO] Tests run: 5000, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.989 s -- in com.example.demos.junit.MyJUnit5000OneInstancePerClassSpec
```

First measurement results for JnrTest:

```text
[  START] MyJnr10
Tests run: 10, Succeeded: 10, Failures: 0, Errors: 0 - Time elapsed: 0.001000 s
[  START] MyJnr100
Tests run: 100, Succeeded: 100, Failures: 0, Errors: 0 - Time elapsed: 0.004000 s
[  START] MyJnr1000
Tests run: 1000, Succeeded: 1000, Failures: 0, Errors: 0 - Time elapsed: 0.015000 s
[  START] MyJnr5000
Tests run: 5000, Succeeded: 5000, Failures: 0, Errors: 0 - Time elapsed: 0.027000 s
```

Second measurement results for JUnit 5:

```text
[INFO] Running io.github.lorenzobettini.jnrtest.core.JnrTestConsoleExecutorTest
[INFO] Tests run: 15, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.718 s -- in io.github.lorenzobettini.jnrtest.core.JnrTestConsoleExecutorTest
[INFO] Running io.github.lorenzobettini.jnrtest.core.JnrTestConsoleParallelExecutorTest
[INFO] Tests run: 9, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.099 s -- in io.github.lorenzobettini.jnrtest.core.JnrTestConsoleParallelExecutorTest
[INFO] Running io.github.lorenzobettini.jnrtest.core.JnrTestConsoleReporterTest
[INFO] Tests run: 13, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.146 s -- in io.github.lorenzobettini.jnrtest.core.JnrTestConsoleReporterTest
[INFO] Running io.github.lorenzobettini.jnrtest.core.JnrTestFilterTest
[INFO] Tests run: 6, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.073 s -- in io.github.lorenzobettini.jnrtest.core.JnrTestFilterTest
[INFO] Running io.github.lorenzobettini.jnrtest.core.JnrTestFiltersTest
[INFO] Tests run: 17, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.040 s -- in io.github.lorenzobettini.jnrtest.core.JnrTestFiltersTest
[INFO] Running io.github.lorenzobettini.jnrtest.core.JnrTestParallelRunnerTest
[INFO] Tests run: 1, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.024 s -- in io.github.lorenzobettini.jnrtest.core.JnrTestParallelRunnerTest
[INFO] Running io.github.lorenzobettini.jnrtest.core.JnrTestRecorderTest
[INFO] Tests run: 7, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.075 s -- in io.github.lorenzobettini.jnrtest.core.JnrTestRecorderTest
[INFO] Running io.github.lorenzobettini.jnrtest.core.JnrTestReporterInterfaceTest
[INFO] Tests run: 4, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.007 s -- in io.github.lorenzobettini.jnrtest.core.JnrTestReporterInterfaceTest
[INFO] Running io.github.lorenzobettini.jnrtest.core.JnrTestRunnerTest
[INFO] Tests run: 12, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.113 s -- in io.github.lorenzobettini.jnrtest.core.JnrTestRunnerTest
[INFO] Running io.github.lorenzobettini.jnrtest.core.JnrTestStatisticsTest
[INFO] Tests run: 7, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.074 s -- in io.github.lorenzobettini.jnrtest.core.JnrTestStatisticsTest
[INFO] Running io.github.lorenzobettini.jnrtest.core.JnrTestThreadSafeConsoleReporterTest
[INFO] Tests run: 5, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.041 s -- in io.github.lorenzobettini.jnrtest.core.JnrTestThreadSafeConsoleReporterTest
[INFO] Running io.github.lorenzobettini.jnrtest.core.JnrTestThreadSafeRecorderTest
[INFO] Tests run: 8, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.109 s -- in io.github.lorenzobettini.jnrtest.core.JnrTestThreadSafeRecorderTest
```

Second measurement results for JnrTest:

```text
[  START] JnrTestConsoleExecutorTest in JnrTest
Tests run: 15, Succeeded: 15, Failures: 0, Errors: 0 - Time elapsed: 0.677000 s
[  START] JnrTestConsoleParallelExecutorTest in JnrTest
Tests run: 9, Succeeded: 9, Failures: 0, Errors: 0 - Time elapsed: 0.063000 s
[  START] JnrTestConsoleReporterTest in JnrTest
Tests run: 13, Succeeded: 13, Failures: 0, Errors: 0 - Time elapsed: 0.128000 s
[  START] JnrTestFilterTest in JnrTest
Tests run: 6, Succeeded: 6, Failures: 0, Errors: 0 - Time elapsed: 0.094000 s
[  START] JnrTestFiltersTest in JnrTest
Tests run: 17, Succeeded: 17, Failures: 0, Errors: 0 - Time elapsed: 0.005000 s
[  START] JnrTestParallelRunnerTest in JnrTest
Tests run: 1, Succeeded: 1, Failures: 0, Errors: 0 - Time elapsed: 0.018000 s
[  START] JnrTestRecorderTest in JnrTest
Tests run: 7, Succeeded: 7, Failures: 0, Errors: 0 - Time elapsed: 0.058000 s
[  START] JnrTestReporterInterfaceTest in JnrTest
Tests run: 4, Succeeded: 4, Failures: 0, Errors: 0 - Time elapsed: 0.001000 s
[  START] JnrTestRunnerTest in JnrTest
Tests run: 12, Succeeded: 12, Failures: 0, Errors: 0 - Time elapsed: 0.091000 s
[  START] JnrTestStatisticsTest in JnrTest
Tests run: 7, Succeeded: 7, Failures: 0, Errors: 0 - Time elapsed: 0.062000 s
[  START] JnrTestThreadSafeConsoleReporterTest in JnrTest
Tests run: 5, Succeeded: 5, Failures: 0, Errors: 0 - Time elapsed: 0.050000 s
[  START] JnrTestThreadSafeRecorderTest in JnrTest
Tests run: 8, Succeeded: 8, Failures: 0, Errors: 0 - Time elapsed: 0.123000 s
```

### Ubuntu runner

Ubuntu 24.04.3 LTS

First measurement results for JUnit 5:

```text
[INFO] Running com.example.demos.junit.MyJUnit10Spec
[INFO] Tests run: 10, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.033 s -- in com.example.demos.junit.MyJUnit10Spec
[INFO] Running com.example.demos.junit.MyJUnit100Spec
[INFO] Tests run: 100, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.154 s -- in com.example.demos.junit.MyJUnit100Spec
[INFO] Running com.example.demos.junit.MyJUnit1000Spec
[INFO] Tests run: 1000, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.726 s -- in com.example.demos.junit.MyJUnit1000Spec
[INFO] Running com.example.demos.junit.MyJUnit5000Spec
[INFO] Tests run: 5000, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 1.491 s -- in com.example.demos.junit.MyJUnit5000Spec
```

First measurement results for JUnit 5 with `@TestInstance(TestInstance.Lifecycle.PER_CLASS)`:

```text
[INFO] Running com.example.demos.junit.MyJUnit10OneInstancePerClassSpec
[INFO] Tests run: 10, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.033 s -- in com.example.demos.junit.MyJUnit10OneInstancePerClassSpec
[INFO] Running com.example.demos.junit.MyJUnit100OneInstancePerClassSpec
[INFO] Tests run: 100, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.149 s -- in com.example.demos.junit.MyJUnit100OneInstancePerClassSpec
[INFO] Running com.example.demos.junit.MyJUnit1000OneInstancePerClassSpec
[INFO] Tests run: 1000, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.591 s -- in com.example.demos.junit.MyJUnit1000OneInstancePerClassSpec
[INFO] Running com.example.demos.junit.MyJUnit5000OneInstancePerClassSpec
[INFO] Tests run: 5000, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 1.226 s -- in com.example.demos.junit.MyJUnit5000OneInstancePerClassSpec
```

First measurement results for JnrTest:

```text
[  START] MyJnr10
Tests run: 10, Succeeded: 10, Failures: 0, Errors: 0 - Time elapsed: 0.001000 s
[  START] MyJnr100
Tests run: 100, Succeeded: 100, Failures: 0, Errors: 0 - Time elapsed: 0.005000 s
[  START] MyJnr1000
Tests run: 1000, Succeeded: 1000, Failures: 0, Errors: 0 - Time elapsed: 0.020000 s
[  START] MyJnr5000
Tests run: 5000, Succeeded: 5000, Failures: 0, Errors: 0 - Time elapsed: 0.031000 s
```

Second measurement results for JUnit 5:

```text
[INFO] Running io.github.lorenzobettini.jnrtest.core.JnrTestFiltersTest
[INFO] Tests run: 17, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.234 s -- in io.github.lorenzobettini.jnrtest.core.JnrTestFiltersTest
[INFO] Running io.github.lorenzobettini.jnrtest.core.JnrTestRunnerTest
[INFO] Tests run: 12, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.659 s -- in io.github.lorenzobettini.jnrtest.core.JnrTestRunnerTest
[INFO] Running io.github.lorenzobettini.jnrtest.core.JnrTestFilterTest
[INFO] Tests run: 6, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.055 s -- in io.github.lorenzobettini.jnrtest.core.JnrTestFilterTest
[INFO] Running io.github.lorenzobettini.jnrtest.core.JnrTestThreadSafeRecorderTest
[INFO] Tests run: 8, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.114 s -- in io.github.lorenzobettini.jnrtest.core.JnrTestThreadSafeRecorderTest
[INFO] Running io.github.lorenzobettini.jnrtest.core.JnrTestConsoleReporterTest
[INFO] Tests run: 13, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.131 s -- in io.github.lorenzobettini.jnrtest.core.JnrTestConsoleReporterTest
[INFO] Running io.github.lorenzobettini.jnrtest.core.JnrTestThreadSafeConsoleReporterTest
[INFO] Tests run: 5, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.039 s -- in io.github.lorenzobettini.jnrtest.core.JnrTestThreadSafeConsoleReporterTest
[INFO] Running io.github.lorenzobettini.jnrtest.core.JnrTestRecorderTest
[INFO] Tests run: 7, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.059 s -- in io.github.lorenzobettini.jnrtest.core.JnrTestRecorderTest
[INFO] Running io.github.lorenzobettini.jnrtest.core.JnrTestParallelRunnerTest
[INFO] Tests run: 1, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.024 s -- in io.github.lorenzobettini.jnrtest.core.JnrTestParallelRunnerTest
[INFO] Running io.github.lorenzobettini.jnrtest.core.JnrTestConsoleExecutorTest
[INFO] Tests run: 15, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.141 s -- in io.github.lorenzobettini.jnrtest.core.JnrTestConsoleExecutorTest
[INFO] Running io.github.lorenzobettini.jnrtest.core.JnrTestConsoleParallelExecutorTest
[INFO] Tests run: 9, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.089 s -- in io.github.lorenzobettini.jnrtest.core.JnrTestConsoleParallelExecutorTest
[INFO] Running io.github.lorenzobettini.jnrtest.core.JnrTestStatisticsTest
[INFO] Tests run: 7, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.079 s -- in io.github.lorenzobettini.jnrtest.core.JnrTestStatisticsTest
[INFO] Running io.github.lorenzobettini.jnrtest.core.JnrTestReporterInterfaceTest
[INFO] Tests run: 4, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.007 s -- in io.github.lorenzobettini.jnrtest.core.JnrTestReporterInterfaceTest
```

Second measurement results for JnrTest:

```text
[  START] JnrTestConsoleExecutorTest in JnrTest
Tests run: 15, Succeeded: 15, Failures: 0, Errors: 0 - Time elapsed: 0.934000 s
[  START] JnrTestConsoleParallelExecutorTest in JnrTest
Tests run: 9, Succeeded: 9, Failures: 0, Errors: 0 - Time elapsed: 0.065000 s
[  START] JnrTestConsoleReporterTest in JnrTest
Tests run: 13, Succeeded: 13, Failures: 0, Errors: 0 - Time elapsed: 0.109000 s
[  START] JnrTestFilterTest in JnrTest
Tests run: 6, Succeeded: 6, Failures: 0, Errors: 0 - Time elapsed: 0.037000 s
[  START] JnrTestFiltersTest in JnrTest
Tests run: 17, Succeeded: 17, Failures: 0, Errors: 0 - Time elapsed: 0.005000 s
[  START] JnrTestParallelRunnerTest in JnrTest
Tests run: 1, Succeeded: 1, Failures: 0, Errors: 0 - Time elapsed: 0.020000 s
[  START] JnrTestRecorderTest in JnrTest
Tests run: 7, Succeeded: 7, Failures: 0, Errors: 0 - Time elapsed: 0.055000 s
[  START] JnrTestReporterInterfaceTest in JnrTest
Tests run: 4, Succeeded: 4, Failures: 0, Errors: 0 - Time elapsed: 0.000000 s
[  START] JnrTestRunnerTest in JnrTest
Tests run: 12, Succeeded: 12, Failures: 0, Errors: 0 - Time elapsed: 0.090000 s
[  START] JnrTestStatisticsTest in JnrTest
Tests run: 7, Succeeded: 7, Failures: 0, Errors: 0 - Time elapsed: 0.061000 s
[  START] JnrTestThreadSafeConsoleReporterTest in JnrTest
Tests run: 5, Succeeded: 5, Failures: 0, Errors: 0 - Time elapsed: 0.035000 s
[  START] JnrTestThreadSafeRecorderTest in JnrTest
Tests run: 8, Succeeded: 8, Failures: 0, Errors: 0 - Time elapsed: 0.107000 s
```

### MacOS runner

MacOS 15.7.2 macos-15-arm64

First measurement results for JUnit 5:

```text
[INFO] Tests run: 10, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.015 s -- in com.example.demos.junit.MyJUnit10Spec
[INFO] Running com.example.demos.junit.MyJUnit100Spec
[INFO] Tests run: 100, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.070 s -- in com.example.demos.junit.MyJUnit100Spec
[INFO] Running com.example.demos.junit.MyJUnit1000Spec
[INFO] Tests run: 1000, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.332 s -- in com.example.demos.junit.MyJUnit1000Spec
[INFO] Running com.example.demos.junit.MyJUnit5000Spec
[INFO] Tests run: 5000, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.588 s -- in com.example.demos.junit.MyJUnit5000Spec
```

First measurement results for JUnit 5 with `@TestInstance(TestInstance.Lifecycle.PER_CLASS)`:

```text
[INFO] Running com.example.demos.junit.MyJUnit10OneInstancePerClassSpec
[INFO] Tests run: 10, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.016 s -- in com.example.demos.junit.MyJUnit10OneInstancePerClassSpec
[INFO] Running com.example.demos.junit.MyJUnit100OneInstancePerClassSpec
[INFO] Tests run: 100, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.075 s -- in com.example.demos.junit.MyJUnit100OneInstancePerClassSpec
[INFO] Running com.example.demos.junit.MyJUnit1000OneInstancePerClassSpec
[INFO] Tests run: 1000, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.231 s -- in com.example.demos.junit.MyJUnit1000OneInstancePerClassSpec
[INFO] Running com.example.demos.junit.MyJUnit5000OneInstancePerClassSpec
[INFO] Tests run: 5000, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.563 s -- in com.example.demos.junit.MyJUnit5000OneInstancePerClassSpec
```

First measurement results for JnrTest:

```text
[  START] MyJnr10
Tests run: 10, Succeeded: 10, Failures: 0, Errors: 0 - Time elapsed: 0.001000 s
[  START] MyJnr100
Tests run: 100, Succeeded: 100, Failures: 0, Errors: 0 - Time elapsed: 0.003000 s
[  START] MyJnr1000
Tests run: 1000, Succeeded: 1000, Failures: 0, Errors: 0 - Time elapsed: 0.007000 s
[  START] MyJnr5000
Tests run: 5000, Succeeded: 5000, Failures: 0, Errors: 0 - Time elapsed: 0.019000 s
```

Second measurement results for JUnit 5:

```text
[INFO] Running io.github.lorenzobettini.jnrtest.core.JnrTestConsoleExecutorTest
[INFO] Tests run: 15, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.717 s -- in io.github.lorenzobettini.jnrtest.core.JnrTestConsoleExecutorTest
[INFO] Running io.github.lorenzobettini.jnrtest.core.JnrTestThreadSafeRecorderTest
[INFO] Tests run: 8, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.298 s -- in io.github.lorenzobettini.jnrtest.core.JnrTestThreadSafeRecorderTest
[INFO] Running io.github.lorenzobettini.jnrtest.core.JnrTestFiltersTest
[INFO] Tests run: 17, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.053 s -- in io.github.lorenzobettini.jnrtest.core.JnrTestFiltersTest
[INFO] Running io.github.lorenzobettini.jnrtest.core.JnrTestConsoleParallelExecutorTest
[INFO] Tests run: 9, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.171 s -- in io.github.lorenzobettini.jnrtest.core.JnrTestConsoleParallelExecutorTest
[INFO] Running io.github.lorenzobettini.jnrtest.core.JnrTestParallelRunnerTest
[INFO] Tests run: 1, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.050 s -- in io.github.lorenzobettini.jnrtest.core.JnrTestParallelRunnerTest
[INFO] Running io.github.lorenzobettini.jnrtest.core.JnrTestStatisticsTest
[INFO] Tests run: 7, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.305 s -- in io.github.lorenzobettini.jnrtest.core.JnrTestStatisticsTest
[INFO] Running io.github.lorenzobettini.jnrtest.core.JnrTestRunnerTest
[INFO] Tests run: 12, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.209 s -- in io.github.lorenzobettini.jnrtest.core.JnrTestRunnerTest
[INFO] Running io.github.lorenzobettini.jnrtest.core.JnrTestReporterInterfaceTest
[INFO] Tests run: 4, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.001 s -- in io.github.lorenzobettini.jnrtest.core.JnrTestReporterInterfaceTest
[INFO] Running io.github.lorenzobettini.jnrtest.core.JnrTestFilterTest
[INFO] Tests run: 6, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.041 s -- in io.github.lorenzobettini.jnrtest.core.JnrTestFilterTest
[INFO] Running io.github.lorenzobettini.jnrtest.core.JnrTestThreadSafeConsoleReporterTest
[INFO] Tests run: 5, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.173 s -- in io.github.lorenzobettini.jnrtest.core.JnrTestThreadSafeConsoleReporterTest
[INFO] Running io.github.lorenzobettini.jnrtest.core.JnrTestConsoleReporterTest
[INFO] Tests run: 13, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.491 s -- in io.github.lorenzobettini.jnrtest.core.JnrTestConsoleReporterTest
[INFO] Running io.github.lorenzobettini.jnrtest.core.JnrTestRecorderTest
[INFO] Tests run: 7, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.229 s -- in io.github.lorenzobettini.jnrtest.core.JnrTestRecorderTest
```

Second measurement results for JnrTest:

```text
[  START] JnrTestConsoleExecutorTest in JnrTest
Tests run: 15, Succeeded: 15, Failures: 0, Errors: 0 - Time elapsed: 0.574000 s
[  START] JnrTestConsoleParallelExecutorTest in JnrTest
Tests run: 9, Succeeded: 9, Failures: 0, Errors: 0 - Time elapsed: 0.120000 s
[  START] JnrTestConsoleReporterTest in JnrTest
Tests run: 13, Succeeded: 13, Failures: 0, Errors: 0 - Time elapsed: 0.338000 s
[  START] JnrTestFilterTest in JnrTest
Tests run: 6, Succeeded: 6, Failures: 0, Errors: 0 - Time elapsed: 0.025000 s
[  START] JnrTestFiltersTest in JnrTest
Tests run: 17, Succeeded: 17, Failures: 0, Errors: 0 - Time elapsed: 0.002000 s
[  START] JnrTestParallelRunnerTest in JnrTest
Tests run: 1, Succeeded: 1, Failures: 0, Errors: 0 - Time elapsed: 0.053000 s
[  START] JnrTestRecorderTest in JnrTest
Tests run: 7, Succeeded: 7, Failures: 0, Errors: 0 - Time elapsed: 0.135000 s
[  START] JnrTestReporterInterfaceTest in JnrTest
Tests run: 4, Succeeded: 4, Failures: 0, Errors: 0 - Time elapsed: 0.000000 s
[  START] JnrTestRunnerTest in JnrTest
Tests run: 12, Succeeded: 12, Failures: 0, Errors: 0 - Time elapsed: 0.088000 s
[  START] JnrTestStatisticsTest in JnrTest
Tests run: 7, Succeeded: 7, Failures: 0, Errors: 0 - Time elapsed: 0.322000 s
[  START] JnrTestThreadSafeConsoleReporterTest in JnrTest
Tests run: 5, Succeeded: 5, Failures: 0, Errors: 0 - Time elapsed: 0.055000 s
[  START] JnrTestThreadSafeRecorderTest in JnrTest
Tests run: 8, Succeeded: 8, Failures: 0, Errors: 0 - Time elapsed: 0.197000 s
```

### Windows runner

Microsoft Windows Server 2025

First measurement results for JUnit 5:

```text
[INFO] Running com.example.demos.junit.MyJUnit10Spec
[INFO] Tests run: 10, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.046 s -- in com.example.demos.junit.MyJUnit10Spec
[INFO] Running com.example.demos.junit.MyJUnit100Spec
[INFO] Tests run: 100, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.178 s -- in com.example.demos.junit.MyJUnit100Spec
[INFO] Running com.example.demos.junit.MyJUnit1000Spec
[INFO] Tests run: 1000, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.952 s -- in com.example.demos.junit.MyJUnit1000Spec
[INFO] Running com.example.demos.junit.MyJUnit5000Spec
[INFO] Tests run: 5000, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 1.851 s -- in com.example.demos.junit.MyJUnit5000Spec
```

First measurement results for JUnit 5 with `@TestInstance(TestInstance.Lifecycle.PER_CLASS)`:

```text
[INFO] Running com.example.demos.junit.MyJUnit10OneInstancePerClassSpec
[INFO] Tests run: 10, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.056 s -- in com.example.demos.junit.MyJUnit10OneInstancePerClassSpec
[INFO] Running com.example.demos.junit.MyJUnit100OneInstancePerClassSpec
[INFO] Tests run: 100, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.179 s -- in com.example.demos.junit.MyJUnit100OneInstancePerClassSpec
[INFO] Running com.example.demos.junit.MyJUnit1000OneInstancePerClassSpec
[INFO] Tests run: 1000, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.704 s -- in com.example.demos.junit.MyJUnit1000OneInstancePerClassSpec
[INFO] Running com.example.demos.junit.MyJUnit5000OneInstancePerClassSpec
[INFO] Tests run: 5000, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 1.682 s -- in com.example.demos.junit.MyJUnit5000OneInstancePerClassSpec
```

First measurement results for JnrTest:

```text
[  START] MyJnr10
Tests run: 10, Succeeded: 10, Failures: 0, Errors: 0 - Time elapsed: 0.001000 s
[  START] MyJnr100
Tests run: 100, Succeeded: 100, Failures: 0, Errors: 0 - Time elapsed: 0.006000 s
[  START] MyJnr1000
Tests run: 1000, Succeeded: 1000, Failures: 0, Errors: 0 - Time elapsed: 0.022000 s
[  START] MyJnr5000
Tests run: 5000, Succeeded: 5000, Failures: 0, Errors: 0 - Time elapsed: 0.036000 s
```

Second measurement results for JUnit 5:

```text
[INFO] Running io.github.lorenzobettini.jnrtest.core.JnrTestConsoleExecutorTest
[INFO] Tests run: 15, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 1.118 s -- in io.github.lorenzobettini.jnrtest.core.JnrTestConsoleExecutorTest
[INFO] Running io.github.lorenzobettini.jnrtest.core.JnrTestConsoleParallelExecutorTest
[INFO] Tests run: 9, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.188 s -- in io.github.lorenzobettini.jnrtest.core.JnrTestConsoleParallelExecutorTest
[INFO] Running io.github.lorenzobettini.jnrtest.core.JnrTestConsoleReporterTest
[INFO] Tests run: 13, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.258 s -- in io.github.lorenzobettini.jnrtest.core.JnrTestConsoleReporterTest
[INFO] Running io.github.lorenzobettini.jnrtest.core.JnrTestFiltersTest
[INFO] Tests run: 17, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.049 s -- in io.github.lorenzobettini.jnrtest.core.JnrTestFiltersTest
[INFO] Running io.github.lorenzobettini.jnrtest.core.JnrTestFilterTest
[INFO] Tests run: 6, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.072 s -- in io.github.lorenzobettini.jnrtest.core.JnrTestFilterTest
[INFO] Running io.github.lorenzobettini.jnrtest.core.JnrTestParallelRunnerTest
[INFO] Tests run: 1, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.045 s -- in io.github.lorenzobettini.jnrtest.core.JnrTestParallelRunnerTest
[INFO] Running io.github.lorenzobettini.jnrtest.core.JnrTestRecorderTest
[INFO] Tests run: 7, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.099 s -- in io.github.lorenzobettini.jnrtest.core.JnrTestRecorderTest
[INFO] Running io.github.lorenzobettini.jnrtest.core.JnrTestReporterInterfaceTest
[INFO] Tests run: 4, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.018 s -- in io.github.lorenzobettini.jnrtest.core.JnrTestReporterInterfaceTest
[INFO] Running io.github.lorenzobettini.jnrtest.core.JnrTestRunnerTest
[INFO] Tests run: 12, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.172 s -- in io.github.lorenzobettini.jnrtest.core.JnrTestRunnerTest
[INFO] Running io.github.lorenzobettini.jnrtest.core.JnrTestStatisticsTest
[INFO] Tests run: 7, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.123 s -- in io.github.lorenzobettini.jnrtest.core.JnrTestStatisticsTest
[INFO] Running io.github.lorenzobettini.jnrtest.core.JnrTestThreadSafeConsoleReporterTest
[INFO] Tests run: 5, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.059 s -- in io.github.lorenzobettini.jnrtest.core.JnrTestThreadSafeConsoleReporterTest
[INFO] Running io.github.lorenzobettini.jnrtest.core.JnrTestThreadSafeRecorderTest
[INFO] Tests run: 8, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.136 s -- in io.github.lorenzobettini.jnrtest.core.JnrTestThreadSafeRecorderTest
```

Second measurement results for JnrTest:

```text
[  START] JnrTestConsoleExecutorTest in JnrTest
Tests run: 15, Succeeded: 15, Failures: 0, Errors: 0 - Time elapsed: 1.250000 s
[  START] JnrTestConsoleParallelExecutorTest in JnrTest
Tests run: 9, Succeeded: 9, Failures: 0, Errors: 0 - Time elapsed: 0.091000 s
[  START] JnrTestConsoleReporterTest in JnrTest
Tests run: 13, Succeeded: 13, Failures: 0, Errors: 0 - Time elapsed: 0.183000 s
[  START] JnrTestFilterTest in JnrTest
Tests run: 6, Succeeded: 6, Failures: 0, Errors: 0 - Time elapsed: 0.050000 s
[  START] JnrTestFiltersTest in JnrTest
Tests run: 17, Succeeded: 17, Failures: 0, Errors: 0 - Time elapsed: 0.007000 s
[  START] JnrTestParallelRunnerTest in JnrTest
Tests run: 1, Succeeded: 1, Failures: 0, Errors: 0 - Time elapsed: 0.022000 s
[  START] JnrTestRecorderTest in JnrTest
Tests run: 7, Succeeded: 7, Failures: 0, Errors: 0 - Time elapsed: 0.085000 s
[  START] JnrTestReporterInterfaceTest in JnrTest
Tests run: 4, Succeeded: 4, Failures: 0, Errors: 0 - Time elapsed: 0.000000 s
[  START] JnrTestRunnerTest in JnrTest
Tests run: 12, Succeeded: 12, Failures: 0, Errors: 0 - Time elapsed: 0.109000 s
[  START] JnrTestStatisticsTest in JnrTest
Tests run: 7, Succeeded: 7, Failures: 0, Errors: 0 - Time elapsed: 0.084000 s
[  START] JnrTestThreadSafeConsoleReporterTest in JnrTest
Tests run: 5, Succeeded: 5, Failures: 0, Errors: 0 - Time elapsed: 0.050000 s
[  START] JnrTestThreadSafeRecorderTest in JnrTest
Tests run: 8, Succeeded: 8, Failures: 0, Errors: 0 - Time elapsed: 0.172000 s
```
