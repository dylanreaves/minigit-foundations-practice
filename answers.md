# HW9 Practice Answers

## Exercise 1 — Predict the output (Linux)

I predicted the following before running the verification program:

| Item | Prediction | Verified on Windows |
| --- | --- | --- |
| a | `repo/.minigit/HEAD` | `repo/.minigit/HEAD` |
| b | `/HEAD` | `/HEAD` |
| c | `notes.txt` | `notes.txt` |
| d | `docs` | `docs` |
| e | `1` | `1` |
| f | `.txt` | `.txt` |

I predicted the output assuming Linux, as instructed, and verified the results using Visual Studio on Windows. All six outputs matched my predictions.

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

## Exercise 4 — Complete the save function

The four blanks are (1) `!file.parent_path().empty()`, (2) `create_directories`, (3) `!output`, and (4) `output.good()`.

```cpp
bool save(const std::filesystem::path& file, const std::string& text) {
    if (!file.parent_path().empty()) {
        std::filesystem::create_directories(file.parent_path());
    }
    std::ofstream output(file);
    if (!output) {
        return false;
    }
    output << text;
    return output.good();
}
```

Here `ofstream` closes the file automatically when it goes out of scope. For production code, I would also handle filesystem exceptions or use the `std::error_code` overload of `create_directories` so folder-creation failures can return `false` rather than throw.
