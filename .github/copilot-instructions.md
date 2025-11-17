# GitHub Copilot Project Instructions

## 1. Project Summary
Project   này   
- <Mô tả ngắn gọn dự án>  
- <Nhiệm vụ chính của chương trình>  
- <Ngôn ngữ và công nghệ sử dụng: C#, WPF, YAML, Python, v.v.>  

Copilot should focus only on the context of this project.

---

## 2. Code Style Guidelines
- Follow the coding style of the project.
- Use clear naming conventions.
- Avoid unnecessary abstractions.
- Prefer readable and maintainable code.
- Keep functions small and focused.

---

## 3. Do / Don’t
### ✔ Do
- Generate code only when requested.
- Use the existing architecture, patterns, and folder structure.
- Follow MVVM (if dự án dùng MVVM).
- Use async/await properly.
- Follow the YAML structure as defined.
- Use existing helper functions instead of rewriting.

### ❌ Don’t
- Do NOT propose technologies outside the project (React, Java, Rust…).
- Do NOT generate entire projects unless asked.
- Do NOT rewrite code unrelated to the request.
- Do NOT change architecture unless user asks explicitly.

---

## 4. YAML Rule (if project uses YAML)
When working with `.yaml` test files:
- Only suggest steps available in `/yaml/steps/*.yaml`.
- Must follow the exact structure:
  
```yaml
- name: <STEP_NAME>
  steps:
    - do: <FUNC_NAME>
      with:
         <key>: <value>
