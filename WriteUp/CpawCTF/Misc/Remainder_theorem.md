## Q26.[PPC]Remainder theorem
### 問題文
```
x ≡ 32134 (mod 1584891)
x ≡ 193127 (mod 3438478)

x = ?


フラグはcpaw{xの値}です！
```
### 解き方
1. 問題文の連立合同式を解くだけ
2. 連立合同式を解くpythonコード
```python
"""Simultaneous congruence solver using the generalized Chinese Remainder Theorem.

Usage examples:
    python3 math/congruence_solver.py 2:3 3:5 2:7
    python3 math/congruence_solver.py 3/4 4/9

Each equation must be given as `remainder:modulus` (a slash `/` can be used
instead of a colon). The script reports the smallest non-negative solution and
the combined modulus. If the system is inconsistent, an error message is shown.
"""

import argparse
from typing import Iterable, List, Tuple


Equation = Tuple[int, int]


def extended_gcd(a: int, b: int) -> Tuple[int, int, int]:
    """Return (g, x, y) such that ax + by = g = gcd(a, b)."""
    old_r, r = a, b
    old_s, s = 1, 0
    old_t, t = 0, 1
    while r:
        quotient = old_r // r
        old_r, r = r, old_r - quotient * r
        old_s, s = s, old_s - quotient * s
        old_t, t = t, old_t - quotient * t
    return old_r, old_s, old_t


def parse_equation(raw: str) -> Equation:
    """Parse a single equation representation."""
    for sep in (":", "/"):
        if sep in raw:
            left, right = raw.split(sep, 1)
            try:
                return int(left.strip()), int(right.strip())
            except ValueError as exc:
                raise ValueError(f"Invalid integer in '{raw}'") from exc
    raise ValueError(f"Unsupported format: '{raw}'")


def normalize_equations(equations: Iterable[Equation]) -> List[Equation]:
    """Ensure all moduli are positive and remainders are reduced."""
    normalized: List[Equation] = []
    for remainder, modulus in equations:
        if modulus == 0:
            raise ValueError("Modulus must be non-zero")
        if modulus < 0:
            modulus = -modulus
            remainder = -remainder
        remainder %= modulus
        normalized.append((remainder, modulus))
    return normalized


def solve_congruences(equations: Iterable[Equation]) -> Tuple[int, int]:
    """Solve x ≡ remainder (mod modulus) for multiple equations.

    Returns:
        (solution, combined_modulus) where solution is the smallest non-negative
        integer satisfying all equations and combined_modulus is the lcm of all
        moduli. Raises ValueError if the system has no solution.
    """
    eq_list = normalize_equations(equations)
    if not eq_list:
        raise ValueError("At least one equation is required")

    solution, modulus = eq_list[0]
    for remainder, current_modulus in eq_list[1:]:
        g, _, _ = extended_gcd(modulus, current_modulus)
        diff = remainder - solution
        if diff % g != 0:
            raise ValueError(
                f"No solution: incompatibility between mod {modulus} and mod {current_modulus}"
            )

        modulus_factor = current_modulus // g
        # Reduce the problem to finding t such that modulus * t ≡ diff (mod current_modulus).
        reduced_target = (diff // g) % modulus_factor
        base = modulus // g
        try:
            base_inverse = pow(base, -1, modulus_factor)
        except ValueError as exc:
            raise ValueError("Failed to compute modular inverse") from exc
        t = (reduced_target * base_inverse) % modulus_factor
        solution = (solution + modulus * t) % (modulus * modulus_factor)
        modulus *= modulus_factor

    return solution % modulus, modulus


def main() -> None:
    parser = argparse.ArgumentParser(description="Solve simultaneous congruences.")
    parser.add_argument(
        "equations",
        nargs="+",
        help="Equations in the form remainder:modulus (e.g. 2:3). You may use '/'.",
    )
    args = parser.parse_args()

    try:
        equations = [parse_equation(item) for item in args.equations]
        solution, modulus = solve_congruences(equations)
    except ValueError as exc:
        parser.error(str(exc))

    print(f"x ≡ {solution} (mod {modulus})")
    print(f"x = {solution} + {modulus}k  (k ∈ ℤ)")


if __name__ == "__main__":
    main()
```
3. フラグ cpaw{35430270439}