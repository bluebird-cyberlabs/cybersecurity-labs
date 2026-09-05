# Linux Process Investigation — Mini SOC Analysis

## Objective

Investigate running processes in a Linux-like Termux environment to determine whether any observed process behavior appears suspicious.

The investigation focuses on:

- Process identification
- PID and PPID relationships
- Process states
- Process ownership
- CPU and memory usage
- Parent-child process relationships
- Process lifetime
- Identification of potentially anomalous behavior

## Environment

- Platform: Android
- Environment: Termux
- Investigation Type: Process analysis
- Primary Tool: `ps`
- Security Context: Mini SOC-style process investigation

## Investigation Scenario

The Termux environment was treated as a potentially unusual system requiring process-level investigation.

The objective was to examine active processes, identify their relationships, observe resource usage, and determine whether any process exhibited characteristics that warranted further investigation.

## Investigation Methodology

1. Enumerating Running Processes

The investigation began with:

```bash
ps aux
```

This provided a process listing containing information such as:

- User
- PID
- CPU usage
- Memory usage
- Process state
- Process start time
- Command

An initially high CPU value was observed for the `com.termux` process.

2. Investigating the Termux Process

The process was examined using:

```bash
ps -p 14325 -o user,pid,ppid,stat,cmd
```

The process was identified as:

```text
- PID: 14325
- PPID: 653
- Command: com.termux
- State: S<l
```

This established the process's parent-child relationship and current process state.

3. Investigating the Parent Process

The parent PID was investigated:

```bash
ps -p 653 -o user,pid,ppid,stat,cmd
```

No active process information was returned.

This demonstrated an important characteristic of process investigation: a PID observed in a process relationship may no longer exist by the time it is investigated because processes can terminate.

4. Investigating Process Activity

The Termux process was searched for directly:

```bash
ps aux | grep com.termux
```

The process was still present, but its CPU usage had decreased significantly.

The observed CPU usage changed from approximately 75.1% during the initial observation to approximately 3.7% later.

This suggested that the initial CPU spike was likely transient rather than sustained.

5. Investigating the Shell Process

The active Bash process was investigated:

```bash
ps -p 14385 -o user,pid,ppid,stat,cmd
```

The result identified:
```text
- PID: 14385
- PPID: 14325
- Command: "/data/data/com.termux/files/usr/bin/bash -l"
- State: "Ss"
```

This established the relationship:

```text
PID 653
   │
   ▼
PID 14325 — com.termux
   │
   ▼
PID 14385 — bash
```

The relationship was consistent with the expected structure of the Termux environment.

6. Investigating Short-Lived Processes

The "grep" command used during investigation appeared as its own process:

```text
PID 15571 — grep com.termux
```

When the PID was investigated afterward, no process information was returned.

This was expected because "grep" is a short-lived command that terminates after completing its search.

7. Measuring Process Lifetime

The Termux process was examined using:

```
ps -p 14325 -o user,pid,ppid,stat,elapsed,cmd
```

The process had an elapsed lifetime of approximately:

```text
24:31
```

This provided additional context when evaluating the earlier CPU spike.

A process that has existed for a significant period but accumulated only a small amount of CPU time is not necessarily performing continuous CPU-intensive activity.

## Findings

The investigation identified the following:


| Observation | Assessment |
|---|---|
| `com.termux` process present | Expected |
| Bash child process present | Expected |
| Parent-child relationships | Consistent |
| Process states | Normal for observed processes |
| Initial high CPU usage | Transient |
| Later CPU usage | Significantly lower |
| Short-lived `grep` process | Expected |
| Parent PID no longer visible | Possible process termination |
| Persistent suspicious process identified | No |


## Final Assessment

Based on the process-table investigation, no obvious suspicious process activity was identified within the visibility provided by the Termux environment.

The observed processes demonstrated expected ownership, parent-child relationships, process states, and resource behavior.

The initially high CPU usage of the "com.termux" process appeared to be transient rather than sustained.

However, this investigation does not prove that the entire Android device is free from malicious activity. Termux has limited visibility into the underlying Android operating system, system services, and processes outside its accessible environment.

## Security Significance

Process investigation is an important defensive security skill because malicious software may create processes, consume system resources, establish parent-child relationships, or attempt to hide within legitimate processes.

Understanding normal process behavior makes it easier to identify anomalies during incident response.

This investigation demonstrates practical experience with:

- Linux process enumeration
- PID/PPID analysis
- Process-state analysis
- Resource monitoring
- Parent-child process relationships
- Short-lived process analysis
- Basic anomaly detection
- SOC-style investigation methodology

## Job-Role Relevance

This type of investigation is relevant to roles such as:

- SOC Analyst
- MSSP Analyst
- Security Operations Analyst
- Junior Linux Administrator
- Junior Cybersecurity Analyst
- Incident Response Trainee

## Tools Used


- Termux
- `ps`
- `grep`
- Bash


## Skills Demonstrated

Technical Skills

- Linux command-line investigation
- Process analysis
- System monitoring
- PID/PPID analysis
- Basic incident investigation

## Analytical Skills

- Establishing process relationships
- Comparing resource usage over time
- Distinguishing expected behavior from potentially anomalous behavior
- Forming evidence-based conclusions
- Recognizing environmental limitations

## Investigation Limitation

This assessment was performed from within Termux on Android.

Because Termux does not provide complete visibility into the Android operating system, the findings should be interpreted as a limited process-level assessment, not a complete host compromise assessment.
