# Step-by-Step Setup for a Dummy Laboratory Work Assignment

Let's create a dummy lab for a student to test how assessments will work. This includes objective evaluation, automated tests, hidden tests, and anti-plagiarism tools.

---

### **Lab Description**
#### **Lab Title**: `CRUD API with H2 Database`
#### **Objective**:
1. Build a REST API using Spring Boot.
2. Implement CRUD operations for a `Student` entity with H2 database integration.
3. Write unit and integration tests for the API.

---

### **Step 1: Create a Template Repository**

1. **Name the Repository**: `lab-dummy-crud-api`.
2. **Structure the Repository**:
   ```
   lab-dummy-crud-api/
   ├── src/
   │   ├── main/
   │   │   ├── java/
   │   │   │   └── com/example/student/
   │   │   │       ├── Student.java
   │   │   │       ├── StudentController.java
   │   │   │       ├── StudentService.java
   │   │   │       └── StudentRepository.java
   │   │   └── resources/
   │   │       ├── application.yml
   │   │       └── data.sql
   │   ├── test/
   │   │   ├── java/
   │   │   │   └── com/example/student/
   │   │   │       ├── StudentControllerTest.java
   │   │   │       ├── HiddenTest.java (hidden later)
   ├── build.gradle.kts
   ├── README.md
   └── .github/workflows/test.yml
   ```

---

### **Step 2: Prepopulate the Template with Starter Code**

#### **1. Entity: `Student`**
```java
package com.example.student;

import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.Id;

@Entity
public class Student {
    @Id
    @GeneratedValue
    private Long id;
    private String name;
    private String email;

    // Getters and Setters
}
```

#### **2. Repository: `StudentRepository`**
```java
package com.example.student;

import org.springframework.data.jpa.repository.JpaRepository;

public interface StudentRepository extends JpaRepository<Student, Long> {
}
```

#### **3. Service: `StudentService`**
```java
package com.example.student;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;

@Service
public class StudentService {
    @Autowired
    private StudentRepository repository;

    public List<Student> getAllStudents() {
        return repository.findAll();
    }

    public Student saveStudent(Student student) {
        return repository.save(student);
    }

    public void deleteStudent(Long id) {
        repository.deleteById(id);
    }
}
```

#### **4. Controller: `StudentController`**
```java
package com.example.student;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/students")
public class StudentController {
    @Autowired
    private StudentService service;

    @GetMapping
    public List<Student> getStudents() {
        return service.getAllStudents();
    }

    @PostMapping
    public Student createStudent(@RequestBody Student student) {
        return service.saveStudent(student);
    }

    @DeleteMapping("/{id}")
    public void deleteStudent(@PathVariable Long id) {
        service.deleteStudent(id);
    }
}
```

---

### **Step 3: Set Up Testing**

#### **1. Regular Tests: `StudentControllerTest`**
```java
package com.example.student;

import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.boot.test.mock.mockito.MockBean;
import org.springframework.test.web.servlet.MockMvc;

import static org.mockito.Mockito.when;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

@WebMvcTest(StudentController.class)
class StudentControllerTest {
    @Autowired
    private MockMvc mockMvc;

    @MockBean
    private StudentService service;

    @Test
    void shouldReturnOkWhenFetchingStudents() throws Exception {
        when(service.getAllStudents()).thenReturn(List.of());
        mockMvc.perform(get("/students"))
                .andExpect(status().isOk());
    }
}
```

#### **2. Hidden Tests: `HiddenTest`**
These tests will not be shared with students but will assess their implementation secretly.

```java
package com.example.student;

import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

import static org.assertj.core.api.Assertions.assertThat;

@SpringBootTest
class HiddenTest {
    @Autowired
    private StudentRepository repository;

    @Test
    void shouldSaveAndFetchStudent() {
        Student student = new Student();
        student.setName("Hidden Test");
        student.setEmail("hidden@test.com");

        repository.save(student);

        assertThat(repository.findAll().size()).isGreaterThan(0);
    }
}
```

#### **3. Integration with H2 Database**
- **`application.yml`**:
  ```yaml
  spring:
    datasource:
      url: jdbc:h2:mem:testdb
      driver-class-name: org.h2.Driver
      username: sa
      password: password
    jpa:
      hibernate:
        ddl-auto: update
  ```

- **`data.sql`** (Preloaded Data):
  ```sql
  INSERT INTO student (name, email) VALUES ('Dummy', 'dummy@student.com');
  ```

---

### **Step 4: Add CI Workflow**

#### **GitHub Actions Workflow**
Create `.github/workflows/test.yml`:
```yaml
name: CI for Lab Assessment

on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Set up JDK 17
        uses: actions/setup-java@v3
        with:
          java-version: '17'

      - name: Build and Test
        run: ./gradlew clean build
```

---

### **Step 5: Anti-Plagiarism Measures**

#### **1. Use Moss (Measure of Software Similarity)**
- Install Moss on your system.
- Run:
  ```bash
  moss -l java -d lab-dummy-crud-api-student-submissions/
  ```
  This compares student submissions for similarity.

#### **2. Git Commit Analysis**
Require students to use Git commits:
- Analyze commit history to see their coding process.
- Look for large, sudden commits (indicates copy-pasting).

#### **3. Randomized Tasks**
- Provide slightly randomized requirements for different students or groups (e.g., change entity attributes).

---

### **Step 6: Simulate a Dummy Student Submission**
1. Use your **student account** to fork the repository.
2. Complete the task as the student (e.g., implement methods and tests).
3. Submit a pull request to the teacher’s repository.

---

### **Step 7: Teacher's Assessment Workflow**
1. Review:
   - **Automated Tests**: Ensure both visible and hidden tests pass.
   - **Manual Review**: Check for clean code, proper comments, and structure.
2. Grade:
   - Use a rubric for consistent grading.
3. Plagiarism:
   - Run Moss and review commit history.

