# FP4
import time
import math
from functools import wraps, reduce
from typing import Iterable

# 1. Логування викликів
def log_calls(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        args_str = ", ".join(repr(arg) for arg in args)
        kwargs_str = ", ".join(f"{k}={v!r}" for k, v in kwargs.items())
        all_args = ", ".join(filter(None, [args_str, kwargs_str]))
        
        print(f"→ {func.__name__}({all_args})")
        
        result = func(*args, **kwargs)
        
        print(f"← {func.__name__} = {result}")
        return result
    return wrapper

@log_calls
def add(a: int, b: int) -> int:
    return a + b

# 2. Вимірювання часу виконання
def timeit(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        start_time = time.perf_counter()
        result = func(*args, **kwargs)
        end_time = time.perf_counter()
        elapsed_ms = (end_time - start_time) * 1000
        print(f"{func.__name__} took ~{elapsed_ms:.1f} ms")
        return result
    return wrapper

@timeit
def slow(n: int) -> int:
    time.sleep(0.05)
    return n * n

# 3. Перевірка «сильного» пароля
def is_strong_password(s: str) -> bool:
    if len(s) < 8:
        return False
    
    has_upper = any(c.isupper() for c in s)
    has_lower = any(c.islower() for c in s)
    has_digit = any(c.isdigit() for c in s)
    has_special = any(not c.isalnum() for c in s)
    
    return all([has_upper, has_lower, has_digit, has_special])

# 4. Чи всі значення «приблизно рівні»?
def all_close(xs: Iterable[float], tol: float = 1e-6) -> bool:
    xs_list = list(xs)
    if not xs_list:
        return True
    
    first = xs_list[0]
    return all(abs(x - first) <= tol for x in xs_list)

# 5. Є хоч одне просте число?
def is_prime(n: int) -> bool:
    if n < 2:
        return False
    if n == 2:
        return True
    if n % 2 == 0:
        return False
    
    return all(n % i != 0 for i in range(3, int(math.sqrt(n)) + 1, 2))

def has_prime(nums: Iterable[int]) -> bool:
    return any(is_prime(n) for n in nums)

# 6. Масштабування і зсув
def scale_and_shift(xs: Iterable[float], scale: float, shift: float) -> list[float]:
    return list(map(lambda x: scale * x + shift, xs))

# 7. Фільтрація e-mail
def filter_emails(items: Iterable[str]) -> list[str]:
    return list(filter(lambda s: '@' in s and '.' in s.split('@', 1)[1], items))

# 8. Частотний словник символів
def char_hist(s: str) -> dict[str, int]:
    return reduce(
        lambda hist, char: {**hist, char: hist.get(char, 0) + 1},
        s,
        {}
    )

# 9. Топ-K студентів за балом
def top_k(students: list[dict[str, int | str]], k: int) -> list[str]:
    sorted_students = sorted(
        students,
        key=lambda s: (-s["score"], s["name"])
    )
    return [student["name"] for student in sorted_students[:k]]

# 10. Нормалізація даних
def minmax_scale(xs: list[float]) -> list[float]:
    if not xs:
        return []
    
    min_val = min(xs)
    max_val = max(xs)
    
    if max_val == min_val:
        return [0.0] * len(xs)
    
    return list(map(lambda x: (x - min_val) / (max_val - min_val), xs))

# Тестування
if __name__ == "__main__":
    print("1. Логування викликів:")
    print(add(2, 3))
    print()
    
    print("2. Вимірювання часу:")
    print(slow(10))
    print()
    
    print("3. Перевірка пароля:")
    print(is_strong_password("Qwerty12!"))  # True
    print(is_strong_password("qwerty12"))   # False
    print()
    
    print("4. Приблизна рівність:")
    print(all_close([1.0, 1.0000005, 0.9999999], tol=1e-5))  # True
    print(all_close([1.0, 1.1], tol=1e-3))                   # False
    print()
    
    print("5. Наявність простих чисел:")
    print(has_prime([4, 6, 8, 9]))     # False
    print(has_prime([4, 6, 7, 9, 10])) # True
    print()
    
    print("6. Масштабування і зсув:")
    print(scale_and_shift([1, 2, 3], 2.0, -1.0))  # [1.0, 3.0, 5.0]
    print()
    
    print("7. Фільтрація e-mail:")
    emails = ["a@b.com", "wrong@", "x@y", "john.doe@mail.org"]
    print(filter_emails(emails))  # ['a@b.com', 'john.doe@mail.org']
    print()
    
    print("8. Частотний словник:")
    print(char_hist("aba c"))  # {'a': 2, 'b': 1, ' ': 1, 'c': 1}
    print()
    
    print("9. Топ-K студентів:")
    students = [{"name":"Ann","score":90},{"name":"Bob","score":95},{"name":"Ada","score":95}]
    print(top_k(students, 2))  # ['Ada', 'Bob']
    print()
    
    print("10. Нормалізація даних:")
    print(minmax_scale([10, 20, 30]))  # [0.0, 0.5, 1.0]
    print(minmax_scale([5, 5, 5]))     # [0.0, 0.0, 0.0]
