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

## Exercise 3 — Fix the two path bugs

**Bug 1:** `root.string() + "/.minigit"` manually constructs a platform-specific path using strings, which can produce mixed separators and defeats portable path construction. **Fix:** use the specific `/` operator on paths.

**Bug 2:** `metadataDirectory(root) / "/HEAD"` has a rooted right-hand operand. On Linux, it discards the metadata directory and produces `/HEAD`. **Fix:** remove the leading slash.

```cpp
std::filesystem::path metadataDirectory(const std::filesystem::path& root) {
    return root / ".minigit";
}
std::filesystem::path headFile(const std::filesystem::path& root) {
    return metadataDirectory(root) / "HEAD";
}
```