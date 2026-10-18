# C++ Module 09 — STL

Three small programs that each use a different STL container for a real problem: a Bitcoin price lookup, a Reverse Polish Notation calculator, and the Ford–Johnson merge-insertion sort (C++98).

*This project was created as part of the 42 curriculum by jpflegha.*

## Exercises

### ex00 — `btc` (Bitcoin exchange) · `std::map`

Loads a CSV database of Bitcoin prices (`data.csv`) into a `std::map<std::string, float>` and reads an input file with lines `date | value`. For every valid line it prints the value multiplied by the exchange rate on that date — or on the **closest earlier date** if the exact date isn't in the database (found with `lower_bound`).

It validates dates (format, month and day ranges including leap years) and values (must be a number between 0 and 1000), and prints a clear error for every bad line.

```bash
cd ex00 && make && ./btc input.txt
2011-01-03 => 3 = 0.9
2011-01-03 => 1.2 = 0.36
Error: not a positive number.
Error: bad input => 2001-42-42
2012-01-11 => 1 = 7.1
Error: too large a number.
```

### ex01 — `RPN` (Reverse Polish Notation) · `std::list` as a stack

Evaluates an expression in Reverse Polish Notation with integers and `+ - * /`. Invalid tokens, too few operands and division by zero print `Error`.

```bash
cd ex01 && make
./RPN "8 9 * 9 - 9 - 9 - 4 - 1 +"
42
./RPN "1 2 * 2 / 2 * 2 4 - +"
0
./RPN "(1 + 1)"
Error
```

### ex02 — `PmergeMe` (merge-insertion sort) · `std::vector` and `std::deque`

Sorts a sequence of positive integers with the **Ford–Johnson merge-insertion** algorithm, once with `std::vector` and once with `std::deque`, and prints the time each container needed:

1. Pair up the elements and order each pair.
2. Recursively sort the pairs by their larger element.
3. Build the main chain from the larger elements and insert the smaller ones with binary search (`std::upper_bound`), in the order given by the **Jacobsthal numbers**, so each search stays within a small range.

This is designed to need very few comparisons.

```bash
cd ex02 && make
./PmergeMe 3 5 9 7 4 1 8 2 6
Before: 3 5 9 7 4 1 8 2 6
After: 1 2 3 4 5 6 7 8 9
Time to process a range of 9 elements with std::vector<int> : 9.00000 us
Time to process a range of 9 elements with std::deque<int>  : 19.00000 us

./PmergeMe $(shuf -i 1-3000 -n 3000 | tr '\n' ' ')
```

Negative numbers or other invalid input print `Error`.

## Key concepts

- Choosing the right STL container for the job (`map`, `list`, `vector`, `deque`)
- Ordered lookups with `std::map::lower_bound`
- Stack-based expression evaluation
- Ford–Johnson merge-insertion sort, binary insertion, Jacobsthal sequence
- Input validation and error handling with exceptions

## Build

```bash
git clone https://github.com/jpfleghaar/CPP09.git
cd CPP09/ex00 && make     # same for ex01 and ex02
```

Each exercise has its own Makefile (`c++ -Wall -Wextra -Werror -std=c++98`).

## Project structure

```
ex00/  BitcoinExchange.{hpp,cpp}, main.cpp, data.csv (database), input.txt (test input)
ex01/  RPN.{hpp,cpp}, main.cpp
ex02/  PmergeMe.{hpp,cpp}, main.cpp
```

## What I learned

- Each STL container has different strengths: `map` for sorted lookups, `vector` for fast random access, `deque` for cheap insertion at both ends.
- The same algorithm can perform quite differently depending on the container.
- Ford–Johnson is one of the most comparison-efficient sorting algorithms; the hard part is getting the insertion order and bounds exactly right.
- Validating every input line carefully is most of the work in real data processing.
