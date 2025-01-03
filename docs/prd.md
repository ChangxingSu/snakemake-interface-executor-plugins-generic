# Product Requirements Document: Snakemake Generic Cluster Executor Plugin

## 1. Overview

### 1.1 Product Vision
Create a universal cluster executor plugin for Snakemake that provides intelligent queue selection and integrated log management, simplifying workflow execution in HPC environments.

### 1.2 Key Features
- Intelligent automatic queue selection
- Integrated log management system
- Seamless Snakemake integration
- Multi-cluster system support

## 2. System Architecture

### 2.1 Overall Architecture
````mermaid
graph TD
    A[Snakemake Workflow] --> B[Generic Executor Plugin]
    B --> C[Queue Selector]
    B --> D[Log Handler]
    B --> E[Job Monitor]
    C --> F[Cluster System]
    D --> F
    E --> F
    F --> D
````

### 2.2 Job Submission Flow
````mermaid
sequenceDiagram
    participant S as Snakemake
    participant E as Executor
    participant Q as Queue Selector
    participant L as Log Handler
    participant C as Cluster

    S->>E: Submit Job
    E->>Q: Get Best Queue
    Q-->>E: Queue Selection
    E->>L: Initialize Logs
    L-->>E: Log Paths
    E->>C: Submit Job
    C-->>E: Job ID
    E->>S: Job Info
````

### 2.3 Log Processing Flow
````mermaid
sequenceDiagram
    participant J as Job
    participant L as Log Handler
    participant E as Executor
    participant S as Snakemake Logger

    J->>L: Generate Output
    L->>L: Split stdout/stderr
    L->>E: Process Logs
    E->>S: Forward Logs
    Note over L,S: Real-time log streaming
````

## 3. Functional Requirements

### 3.1 Queue Selection System
- **Automatic Queue Matching**
  - Based on job resource requirements
  - Considers queue load
  - Supports priority rules
  
- **Resource Management**
  - Memory limit validation
  - CPU core allocation
  - Runtime control

### 3.2 Log Management System
- **Log File Processing**
  ```yaml
  # Internal logging configuration
  logging:
    base_dir: "{workflow_dir}/logs"
    patterns:
      stdout: "{rule}.{jobid}.out"
      stderr: "{rule}.{jobid}.err"
    merge_on_completion: true
  ```

- **Log Stream Processing**
  - Real-time log capture
  - Error detection and reporting
  - Log file rotation

### 3.3 Configuration System
```yaml
# config.yaml
executor:
  queue_config:
    default_queue: "short"
    auto_select: true
    selection_criteria:
      - resource_match
      - load_balance
      - priority_weight

  logging:
    enabled: true
    level: "INFO"
    format: "{timestamp} [{level}] {message}"
```

## 4. Technical Implementation

### 4.1 Core Components

#### Queue Selector
```python
class QueueSelector:
    def select_queue(self, resources, constraints):
        """
        Select the best queue based on job resource requirements
        """
        pass
```

#### Log Handler
```python
class LogHandler:
    def handle_job_logs(self, job_info):
        """
        Handle job log files
        - Create log directories
        - Set log paths
        - Configure log redirection
        """
        pass
```

### 4.2 Integration Points

#### Snakemake Interface
```python
class GenericExecutor(RemoteExecutor):
    def run_job(self, job):
        """
        Handle job submission
        1. Queue selection
        2. Log initialization
        3. Job submission
        4. Status monitoring
        """
        pass
```

## 5. Development Phases

### Phase 1: Core Framework (Week 1-2)
- [x] Basic executor framework
- [x] Configuration system
- [x] Testing framework

### Phase 2: Queue Management (Week 3-4)
- [ ] Queue selection algorithm
- [ ] Resource validation
- [ ] Load balancing

### Phase 3: Log System (Week 5-6)
- [ ] Log handler
- [ ] Real-time log streaming
- [ ] Error handling

### Phase 4: Testing & Documentation (Week 7-8)
- [ ] Integration testing
- [ ] Performance testing
- [ ] Documentation writing

## 6. Success Metrics

### 6.1 Performance Metrics
- Queue selection accuracy > 95%
- Log processing latency < 1s
- Job submission success rate > 99%

### 6.2 Quality Metrics
- Code test coverage > 90%
- Documentation completeness > 95%
- User satisfaction > 90%

## 7. Constraints & Assumptions

### 7.1 Constraints
- No modifications to Snakemake core functionality
- Maintain backward compatibility
- Minimize performance overhead

### 7.2 Assumptions
- Cluster systems support job log redirection
- Sufficient file system permissions
- Stable network connectivity

## 8. Future Enhancements

### 8.1 Planned Features
- Advanced queue scheduling strategies
- Log analysis tools
- Performance optimization tools
- Web interface support

### 8.2 Potential Extensions
- Cloud platform integration
- Container support
- Distributed logging system


## 9. Technical Specifications

### 9.1 Supported Cluster Systems
- Slurm
- LSF
- PBS/Torque
- SGE/UGE

### 9.2 Resource Mapping
```yaml
resource_mapping:
  mem_mb:
    slurm: "--mem={}"
    lsf: "-M {}"
    pbs: "-l mem={}mb"
  time_min:
    slurm: "--time={}"
    lsf: "-W {}"
    pbs: "-l walltime={}:00"
```

### 9.3 Error Handling
- Job submission failures
- Resource allocation errors
- Queue access issues
- Log file errors

## 10. Security Considerations

### 10.1 Authentication
- Cluster credentials management
- Permission validation
- Secure log handling

### 10.2 Data Protection
- Log file permissions
- Temporary file cleanup
- Sensitive information handling
