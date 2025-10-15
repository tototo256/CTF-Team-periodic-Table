## Q29.[Crypto] Common World
### 問題文
```
Cpaw君は,以下の公開鍵を用いて暗号化された暗号文Cを受け取りました．しかしCpaw君は秘密鍵を忘れてしまいました.Cpaw君のために暗号文を解読してあげましょう.

(e, N) = (11, 236934049743116267137999082243372631809789567482083918717832642810097363305512293474568071369055296264199854438630820352634325357252399203160052660683745421710174826323192475870497319105418435646820494864987787286941817224659073497212768480618387152477878449603008187097148599534206055318807657902493850180695091646575878916531742076951110529004783428260456713315007812112632429296257313525506207087475539303737022587194108436132757979273391594299137176227924904126161234005321583720836733205639052615538054399452669637400105028428545751844036229657412844469034970807562336527158965779903175305550570647732255961850364080642984562893392375273054434538280546913977098212083374336482279710348958536764229803743404325258229707314844255917497531735251105389366176228741806064378293682890877558325834873371615135474627913981994123692172918524625407966731238257519603614744577)

暗号文: 80265690974140286785447882525076768851800986505783169077080797677035805215248640465159446426193422263912423067392651719120282968933314718780685629466284745121303594495759721471318134122366715904

これは…？

フラグは以下のシンタックスです.
cpaw{復号した値}

※復号した値はそのままで良いですが，実は意味があります，余力がある人は考えてみてください.
```
### 解き方
1. $C < M^e$より，Cのe乗根をとればMを手に入れられる
2. 小平文攻撃を行うpythonコード
```python
"""
Usage examples:
    python3 rsa.py --e 3 --c 74088
    python3 rsa.py --n 90581 --e 3 --c 5489031744

The script checks whether the ciphertext is a perfect `e`-th power, and when it
is, prints the plaintext in multiple representations. If `n` is provided, the
script also verifies that `m^e < n`, which must hold for the attack to be valid.
"""

from __future__ import annotations

import argparse
from typing import Tuple


def integer_nth_root(value: int, n: int) -> Tuple[int, bool]:
    """Compute the integer n-th root of value and report if it is exact.

    Returns:
        (root, is_exact) where root**n <= value and
        root is the greatest integer with that property.
    """
    if value < 0:
        raise ValueError("Ciphertext must be non-negative for this attack.")
    if n <= 0:
        raise ValueError("Exponent must be positive.")
    if value in (0, 1):
        return value, True

    # Locate an upper bound for the root.
    low, high = 0, 1
    while pow(high, n) <= value:
        high <<= 1

    # Binary search to find the floor n-th root.
    while low + 1 < high:
        mid = (low + high) // 2
        mid_power = pow(mid, n)
        if mid_power == value:
            return mid, True
        if mid_power < value:
            low = mid
        else:
            high = mid

    return low, pow(low, n) == value


def long_to_bytes(value: int) -> bytes:
    if value < 0:
        raise ValueError("Plaintext cannot be negative.")
    length = max(1, (value.bit_length() + 7) // 8)
    return value.to_bytes(length, "big")


def decode_plaintext(m: int) -> Tuple[int, str, str]:
    plaintext_bytes = long_to_bytes(m)
    try:
        ascii_repr = plaintext_bytes.decode("utf-8")
    except UnicodeDecodeError:
        ascii_repr = plaintext_bytes.decode("utf-8", errors="replace")
    return m, plaintext_bytes.hex(), ascii_repr


def main() -> None:
    parser = argparse.ArgumentParser(description="RSA small plaintext (e-th root) attack helper.")
    parser.add_argument("--c", type=int, required=True, help="Ciphertext (integer).")
    parser.add_argument("--e", type=int, default=3, help="Public exponent e (default: 3).")
    parser.add_argument("--n", type=int, help="Modulus n (optional, used for sanity check).")
    args = parser.parse_args()

    root, is_exact = integer_nth_root(args.c, args.e)
    if not is_exact:
        parser.error("Ciphertext is not a perfect e-th power; cannot recover plaintext.")

    if args.n is not None and pow(root, args.e) >= args.n:
        parser.error("m^e >= n. Low exponent attack conditions are not met.")

    m_int, m_hex, m_ascii = decode_plaintext(root)
    print("Plaintext (int):", m_int)
    print("Plaintext (hex):", m_hex or "00")
    print("Plaintext (utf-8):", m_ascii)


if __name__ == "__main__":
    main()
```
3. このコードを実行すると解読結果が表示されるので，intの結果をコピーし，cpaw{}に入れるとそれがフラグとなる．
4. フラグ cpaw{424311244315114354}