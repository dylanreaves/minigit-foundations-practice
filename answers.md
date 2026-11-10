# HW9 Practice Answers

## Exercise 1 — Predict the output (Linux)

I predicted the following before running the verification program:

| Item | Prediction | Verified on Linux |
| --- | --- | --- |
| a | `repo/.minigit/HEAD` | `repo/.minigit/HEAD` |
| b | `/HEAD` | `/HEAD` |
| c | `notes.txt` | `notes.txt` |
| d | `docs` | `docs` |
| e | `1` | `1` |
| f | `.txt` | `.txt` |

All six predictions matched the output of `ex1_verify.cpp`. In (b), the right-hand operand begins at the root, so it replaces `repo` instead of being appended to it. The test was run on Linux as required by the question.

## Exercise 2 — Fix the lost newlines

`std::getline` removes the newline delimiter from each line. Appending only `line` turns `"a\nb\n"` into `"ab"`. Reading the entire stream buffer preserves the original contents, including the final newline.

```cpp
std::string readAll(const std::filesystem::path& file) {
    std::ifstream input(file, std::ios::binary);
    if (!input) {
        throw std::runtime_error("Cannot open file: " + file.string());
    }
    std::ostringstream contents;
    contents << input.rdbuf();
    if (input.bad() || !contents) {
        throw std::runtime_error("Cannot read file: " + file.string());
    }
    return contents.str();
}
```

This version needs `<fstream>`, `<sstream>`, and `<stdexcept>`. Binary mode avoids newline translation when the goal is to preserve exact bytes.