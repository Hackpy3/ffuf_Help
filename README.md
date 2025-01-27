# ffuf_Help





FFUF (Fuzz Faster U Fool) is a powerful tool for web fuzzing, often used to discover directories, files, parameters, and vulnerabilities. Recursion is a technique within FFUF to handle nested directories or structures, allowing the tool to explore deeper paths that were found during the fuzzing process. 

Here's an explanation of **recursion strategies** and best practices when using FFUF:

---

### **Key FFUF Options for Recursion**
1. **`-recursion`**  
   Enables recursive scanning. When FFUF finds a directory, it will use the same wordlist to fuzz that directory.

2. **`-recursion-depth <number>`**  
   Specifies how many levels deep FFUF should recursively fuzz. For instance, a depth of `2` means FFUF will go two levels deep into discovered directories.

3. **`-recursion-strategy`**  
   Determines how FFUF decides which paths to recursively fuzz. Common strategies:
   - **`default`**: Recursively fuzz only if the response matches the filters (e.g., status codes, size).
   - **`greedy`**: Always recursively fuzz, regardless of whether the response matches the filters.

4. **Filters (`-fc`, `-fs`, etc.)**  
   Apply filters (e.g., by status code, response size, or word count) to reduce noise. These filters influence what gets recursively fuzzed when combined with the `-recursion-strategy default`.

---

### **Recursion Strategy Recommendations**

#### **1. Default Strategy**
- Best for targeted discovery.
- Use this when you want to avoid unnecessary fuzzing in non-relevant directories.
- Example:
  ```bash
  ffuf -u http://example.com/FUZZ -w wordlist.txt -recursion -recursion-depth 3 -recursion-strategy default -mc 200
  ```
  - Here, FFUF will only recursively fuzz directories that return status code 200.

#### **2. Greedy Strategy**
- Use this when you want comprehensive coverage of all discovered paths, regardless of initial filters.
- Useful in exploratory testing or when dealing with unknown server behavior.
- Example:
  ```bash
  ffuf -u http://example.com/FUZZ -w wordlist.txt -recursion -recursion-depth 3 -recursion-strategy greedy
  ```

#### **3. Combining Filters with Default Strategy**
- Pair `-recursion-strategy default` with filters to focus on directories likely to be valid or interesting.
- Example:
  ```bash
  ffuf -u http://example.com/FUZZ -w wordlist.txt -recursion -recursion-depth 2 -mc 200 -fs 0
  ```
  - Here, FFUF will recursively fuzz only directories that return a 200 status code and avoid empty responses.

---

### **General Tips**
1. **Choose Depth Carefully**  
   Large recursion depths can significantly increase runtime. Start with 2–3 and increase as needed.

2. **Refine Wordlists**  
   Use wordlists tailored for the application or server type to improve results.

3. **Filter Aggressively**  
   Avoid wasting resources by filtering irrelevant responses. Use:
   - `-mc` for matching specific status codes.
   - `-fc` to filter out common responses (e.g., 404).
   - `-fs` to filter by response size.

4. **Save Results**  
   Use the `-o` flag to save the output for analysis:
   ```bash
   ffuf -u http://example.com/FUZZ -w wordlist.txt -recursion -o output.json -of json
   ```

5. **Parallel Fuzzing**  
   Use `-rate` and `-t` options to optimize performance, especially with deeper recursion:
   ```bash
   ffuf -u http://example.com/FUZZ -w wordlist.txt -recursion -t 50 -rate 100
   ```

By understanding these strategies and combining them with intelligent filtering, you can use FFUF's recursion feature effectively for comprehensive web discovery.
