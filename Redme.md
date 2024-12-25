# Detailed Example for Converting Software Requirements to PlantUML Class Diagram

## Software Requirements

The attendance system is designed to streamline employee attendance management for organizations. The system should allow the HR department to register new employees with details such as Employee ID, Name, Position, Department, and Contact Information. The system should enable employees to mark their attendance via biometric or web interface, recording timestamps. HR or managers should be able to generate attendance reports for employees for a given time period. Additionally, employees should be able to request leave, and managers should have the ability to approve or reject these requests. The system should also notify employees and managers about pending leave requests, attendance anomalies, and reminders.

---

## Step 1: NLP Using SpaCy

### Named Entity Recognition (NER)
Extracted entities:
```
'HR department', 'employees', 'Employee ID', 'Name', 'Position', 'Department', 'Contact Information',
'attendance', 'biometric', 'web interface', 'timestamp', 'managers', 'attendance reports',
'time period', 'leave requests', 'notifications'.
```

### Tokenization and Stop Word Removal
- **Important Tokens after Stop Word Removal (Input Tokens):**
  ```
  ['HR', 'register', 'employees', 'Employee ID', 'Name', 'Position', 'Department', 'Contact Information',
  'attendance', 'mark', 'biometric', 'web', 'timestamp', 'managers', 'reports',
  'time period', 'leave', 'approve', 'reject', 'notify', 'pending', 'anomalies', 'reminders'].
  ```

---

## Step 2: Syntax Tree Representation of the Expected PlantUML

### Expected PlantUML Class Diagram:
```plantuml
@startuml
Class Employee {
    String EmployeeID
    String Name
    String Position
    String Department
    String ContactInfo
}
Class Attendance {
    Timestamp MarkedTime
}
Class Report {
    Date StartDate
    Date EndDate
}
Class LeaveRequest {
    Boolean Approved
    String Reason
}
Class Notification {
    String Message
}

Employee "1" - "*" Attendance
Employee "1" - "*" LeaveRequest
LeaveRequest "1" - "*" Notification
Notification "1" - "*" Employee
@enduml
```
### Abstract Syntax Tree Representation of the Given PlantUML Code

```
Root: Diagram
├── Class: Employee
│   ├── Attribute: String EmployeeID
│   ├── Attribute: String Name
│   ├── Attribute: String Position
│   ├── Attribute: String Department
│   └── Attribute: String ContactInfo
├── Class: Attendance
│   └── Attribute: Timestamp MarkedTime
├── Class: Report
│   ├── Attribute: Date StartDate
│   └── Attribute: Date EndDate
├── Class: LeaveRequest
│   ├── Attribute: Boolean Approved
│   └── Attribute: String Reason
├── Class: Notification
│   └── Attribute: String Message
├── Relationship: Association
│   ├── Source: Employee
│   └── Target: Attendance
├── Relationship: Association
│   ├── Source: Employee
│   └── Target: LeaveRequest
├── Relationship: Association
│   ├── Source: LeaveRequest
│   └── Target: Notification
└── Relationship: Association
    ├── Source: Notification
    └── Target: Employee
```


### Converted Syntax Tree Representation:
1. **Root**: Diagram
     - **Children**: Classes (Employee, Attendance, Report, LeaveRequest, Notification).  
     - **Employee** has attributes: EmployeeID, Name, Position, Department, ContactInfo.  
     - **Attendance** has attributes: MarkedTime.  
     - **Report** has attributes: StartDate, EndDate.  
     - **LeaveRequest** has attributes: Approved, Reason.  
     - **Notification** has attributes: Message.  

---

## Step 3: Transformer Training (Seq2AST)

- **Input Positional Encoding**: Each token in the software requirements is assigned a position. Example:
  ```
  "The system should allow HR to register" -> [1, 2, 3, 4, 5].
  ```
- **Output Byte-Pair Encoding (BPE)**: Processed to subwords. Example:
"The system should allow the HR department to register new employees with details such as Employee ID, Name, Position, Department, and Contact Information."

BPE Process:

1. **Initial Step**: Start with individual characters as the initial vocabulary.
2. **Merging Pairs**: Repeated character pairs are merged iteratively into new subword units (tokens).
3. **Stop Condition**: The process stops when no more frequent pairs are found or after a defined number of merges.

### Example of Tokenized Output after BPE:

```json
{
    "tokens": {
        "The": 1001,
        "sys": 1002,
        "tem": 1003,
        "should": 1004,
        "allow": 1005,
        "the": 1006,
        "HR": 1007,
        "depart": 1008,
        "ment": 1009,
        "to": 1010,
        "reg": 1011,
        "ister": 1012,
        "new": 1013,
        "empl": 1014,
        "oyees": 1015,
        "with": 1016,
        "det": 1017,
        "ails": 1018,
        "such": 1019,
        "as": 1020,
        "Emp": 1021,
        "loyee": 1022,
        "ID": 1023,
        "Nam": 1024,
        "e": 1025,
        "Pos": 1026,
        "ition": 1027,
        "Depar": 1028,
        "tment": 1029,
        "and": 1030,
        "Con": 1031,
        "tact": 1032,
        "In": 1033,
        "format": 1034,
        "ion": 1035
    }
}
```

---

## Step 4: FPGrowth Training

From patterns observed in the data:
- **Discovered Pattern**: If a class **Employee** exists and there is a class **Attendance**, then an inferred attribute **workingHours** should be added to the class **Attendance** to calculate employee hours.

---

## Step 5: Testing Stage

1. **Processed Input**: OOV tokens replaced with `<UNK>`.
   Example:
   ```
   "Employees mark attendance via a web interface."  
   -> "Employees <UNK> attendance <UNK> <UNK> web interface."
   ```

2. **Generated Sequential Text**:  
   ```
   Class Employee has Attribute EmployeeID and Attribute Name and Attribute Position and Attribute Department and Attribute ContactInfo.  
   Class Attendance has Attribute MarkedTime and Attribute workingHours.  
   Class Report has Attribute StartDate and Attribute EndDate.  
   Class LeaveRequest has Attribute Approved and Attribute Reason.  
   Class Notification has Attribute Message.  
   Employee has Relationship Association with Attendance.  
   Employee has Relationship Association with LeaveRequest.  
   LeaveRequest has Relationship Association with Notification.  
   Notification has Relationship Association with Employee.
   ```

3. **Evaluation Using Metrics**:
   - **BLEU Score Calculation**:
     - **Reference Text**:
       ```
       Class Employee has Attribute EmployeeID and Attribute Name and Attribute Position and Attribute Department and Attribute ContactInfo.  
       Class Attendance has Attribute MarkedTime.  
       Class Report has Attribute StartDate and Attribute EndDate.  
       Class LeaveRequest has Attribute Approved and Attribute Reason.  
       Class Notification has Attribute Message.  
       Employee has Relationship Association with Attendance.  
       Employee has Relationship Association with LeaveRequest.  
       LeaveRequest has Relationship Association with Notification.  
       Notification has Relationship Association with Employee.
       ```

     - **Generated Text (from seq2ast model)**:
       ```
       Class Employee has Attribute EmployeeID and Attribute Name and Attribute Position and Attribute Department and Attribute ContactInfo.  
       Class Attendance has Attribute MarkedTime and Attribute workingHours.  
       Class Report has Attribute StartDate and Attribute EndDate.  
       Class LeaveRequest has Attribute Approved and Attribute Reason.  
       Class Notification has Attribute Message.  
       Employee has Relationship Association with Attendance.  
       Employee has Relationship Association with LeaveRequest.  
       LeaveRequest has Relationship Association with Notification.  
       Notification has Relationship Association with Employee.
       ```

     - **BLEU Calculation**:
       - **1-gram Precision**: \( \frac{21}{22} = 0.954 \)  
       - **2-gram Precision**: \( \frac{18}{20} = 0.9 \)  
       - **3-gram Precision**: \( \frac{15}{18} = 0.833 \)  
       - **4-gram Precision**: \( \frac{12}{16} = 0.75 \)  

       **BLEU Score**:
       ```
       BLEU = BP * exp((log(0.954) + log(0.9) + log(0.833) + log(0.75)) / 4) = 0.85.
       ```

   - **Recall**:
     ```
     Recall = \frac{\text{Correctly Generated Elements}}{\text{Total Elements in Ground Truth}} = \frac{18}{20} = 0.9.
     ```

   - **Precision**:
     ```
     Precision = \frac{\text{Correctly Generated Elements}}{\text{Total Elements Generated}} = \frac{18}{21} = 0.857.
     ```

   - **Over-Specification**:
     ```
     Over-Specification = \frac{\text{Extra Elements}}{\text{Total Elements Generated}} \times 100\% = \frac{1}{21} \times 100 = 4.76\%.
     ```

---
## Formatting the sequential text to the PlantUML code

 ```
# Algorithm: Seq2PlantUML

# Initialize classes and relationships data structures
Initialize classes as empty dictionary
Initialize relationships as empty list

# Define class pattern and relationship pattern
Define class pattern as "Class <ClassName> has Attribute <AttributeName>"
Define relationship pattern as "<Class1> has Relationship Association with <Class2>"

# Process each line in the input text
For each line in input_text:
    # If line matches the class pattern
    If line matches class pattern:
        Extract class name and attribute from line
        If class name not in classes:
            Initialize class in classes with an empty list of attributes
        Add attribute and its inferred type to the class's list of attributes
    
    # If line matches the relationship pattern
    If line matches relationship pattern:
        Extract class1 and class2 from line
        Add the relationship (class1, class2) to the relationships list

# Generate PlantUML code
Start with "@startuml"

# For each class in classes, generate the class block
For each class in classes:
    Add "Class <ClassName> {"
    For each attribute in class's attributes:
        Add "<AttributeType> <AttributeName>"
    Close the class block with "}"
    
# For each relationship in relationships, add the relationship to PlantUML
For each relationship in relationships:
    Add "<Class1> '1' - '*' <Class2>"

# End the PlantUML code
End with "@enduml"

# Return or output the final PlantUML code
Return the generated PlantUML code

 ```

![Example Image](https://github.com/FeisalAlaswad/GenClass-Running-Example/blob/main/example.png)

---


## Step 6: Completeness, Correctness, UML Adherence

1. **Completeness**:
   - Missing: The `workingHours` attribute in Attendance is extra.  
   - Completeness score:  
     ```
     $$
\text{Completeness} = \frac{\text{Correct Elements}}{\text{Total Expected Elements}} = \frac{18}{20} = 0.9
$$
     ```

2. **Correctness**:
   - Correct relationships and attributes: \( \text{Score} = 1 \).  

3. **UML Adherence**:  
   - Fully adheres to UML standards (100%).

---

## Final Results

| Metric               | Value         |
|-----------------------|---------------|
| BLEU Score           | **0.85**      |
| Recall               | **90%**       |
| Precision            | **85.7%**     |
| Over-Specification   | **4.76%**     |
| Completeness         | **90%**       |
| Correctness          | **100%**      |
| UML Adherence        | **100%**      |

