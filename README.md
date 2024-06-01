
# Abhimanyu in Chakravyuha

This project implements an algorithm to determine if Abhimanyu can cross all 11 circles in a Chakravyuha while facing various enemies with different power levels. Abhimanyu starts from the innermost circle and has to cross all 11 circles to reach the Pandavas' army back.

## Problem Statement

Given:
- Each circle is guarded by a different enemy with power levels `k1, k2, ..., k11`.
- Abhimanyu starts from the innermost circle with initial power `p`.
- Abhimanyu has a boon to skip fighting an enemy `a` times.
- Abhimanyu can recharge himself with power `b` times.
- Battling in each circle will result in a loss of the same power from Abhimanyu as the enemy.
- If Abhimanyu enters the circle with energy less than the respective enemy, he will lose the battle.
- Enemies in circles 3 and 7 can regenerate themselves once with half of their initial power and can attack Abhimanyu from behind if he is battling in the next circle.

## Algorithm

The algorithm uses dynamic programming with memoization to determine if Abhimanyu can cross all the circles. The approach involves recursively trying to cross each circle, while considering skips and recharges, and updating the state accordingly.

### Variables

- `cross`: A boolean flag indicating whether Abhimanyu can cross all 11 circles.
- `k`: An integer representing Abhimanyu's initial power.
- `dpp`: A 2D vector used for memoization to store the results of subproblems.
- `arr`: A vector containing the power levels of enemies in each of the 11 circles.
- `idx`: An integer representing the current circle Abhimanyu is trying to cross.
- `p`: A double representing Abhimanyu's current power level.
- `b`: An integer representing the number of recharges Abhimanyu has left.
- `a`: An integer representing the number of skips Abhimanyu has left.
- `prev`: An integer used to track the previous circle (default is -1).
- `k3` and `k7`: Integers used to handle the special regeneration properties of enemies in circles 3 and 7.

### Process

1. **Base Cases**:
   - If `idx` is greater than or equal to the size of `arr`, set `cross` to true, indicating Abhimanyu has successfully crossed all circles.
   - If Abhimanyu has no power, recharges, or skips left, return 0.
   - If a subproblem result is already computed (`dpp[idx][prev + 1] != -1`), return the stored result.

2. **Special Conditions for k3 and k7**:
   - If Abhimanyu reaches circle 3 (`idx == 2`), set `k3` to 2.
   - If Abhimanyu reaches circle 7 (`idx == 6`), set `k7` to 2.

3. **Recursive Calls**:
   - Calculate `donttake`, which is the result if Abhimanyu skips the current circle (`a > 0`).
   - Calculate the enemy's power, considering the special regeneration conditions if the previous circle was 3 or 7.
   - If Abhimanyu's power is less than the enemy's power and he has recharges left, recharge his power (`p = k`) and decrease the number of recharges (`b--`).
   - Subtract the enemy's power from Abhimanyu's power and make a recursive call to the next circle.
   - Store the result of the maximum value between `take` and `donttake` in the memoization table `dpp`.

4. **Memoization**:
   - Store the result in `dpp` to avoid recomputation of the same subproblem.

## Implementation

The main function `dp` performs the dynamic programming logic, while `runTestCases` runs predefined test cases to validate the solution.

```cpp
#include <bits/stdc++.h>
using namespace std;

bool cross = false;
int k;

int dp(vector<vector<int>>& dpp, vector<int> &arr, int idx, double p, int b, int a, int prev = -1, int k3 = 1, int k7 = 1) {
    if (idx >= arr.size()) {
        cross = true;
        return 0;
    }
    if (cross == true) return 0;
    if (p == 0 && b == 0 && a == 0) return 0;
    if (dpp[idx][prev + 1] != -1) return dpp[idx][prev + 1];
    if (idx == 2) k3 = 2;
    if (idx == 6) k7 = 2;
    int take = 0;
    int donttake = 0;
    if (a > 0) donttake = dp(dpp, arr, idx + 1, p, b, a - 1, prev, k3, k7);
    double enemy = arr[idx];
    if (idx - 1 == 2) enemy += arr[idx - 1] / k3;
    if (idx - 1 == 6) enemy += arr[idx - 1] / k7;
    if (p - enemy < 0 && b > 0) {
        p = k;
        b--;
    }
    p = p - enemy;
    if (p >= 0) take = 1 + dp(dpp, arr, idx + 1, p, b, a, prev, k3, k7);
    return dpp[idx][prev + 1] = max(take, donttake);
}

void runTestCases() {
    vector<vector<int>> testCases = {
        // Test Case 1: High initial power, low skips, low recharges, strong enemies
        {100, 250, 200, 250, 360, 350, 250, 400, 250, 150, 100},

        // Test Case 2: Low initial power, high skips, and high recharges with moderate enemies
        {11, 23, 16, 21, 34, 35, 20, 34, 12, 40, 32}
    };

    vector<tuple<int, int, int>> parameters = {
        {1100, 1, 2},  // Params for Test Case 1:  Initial Power, Skips, Recharges
        {60, 4, 5} // Params for Test Case 2: Initial Power, Skips, Recharges
    };

    for (int i = 0; i < testCases.size(); ++i) {
        cross = false;
        auto& p = testCases[i];
        auto [initialPower, skips, recharges] = parameters[i];
        vector<vector<int>> dpp(11, vector<int>(12, -1));
        k = initialPower;
        int result = dp(dpp, p, 0, initialPower, recharges, skips);
        cout << "Test Case " << i + 1 << ": " << (cross ? "Abhimanyu can cross" : "Abhimanyu cannot cross") << endl;
    }
}

int main() {
    runTestCases();
    return 0;
}
```

## Explanation of Test Cases

### Test Case 1: High initial power, low skips, low recharges, strong enemies
- **Enemies**: {100, 250, 200, 250, 360, 350, 250, 400, 250, 150, 100}
- **Initial Power**: 1100
- **Skips**: 1
- **Recharges**: 2
- **Expected Outcome**: Abhimanyu can cross

### Test Case 2: Low initial power, high skips, and high recharges with moderate enemies
- **Enemies**: {11, 23, 16, 21, 34, 35, 20, 34, 12, 40, 32}
- **Initial Power**: 60
- **Skips**: 4
- **Recharges**: 5
- **Expected Outcome**: Abhimanyu can cross

These test cases validate the algorithm's ability to handle different initial conditions and enemy configurations. By adjusting parameters such as initial power, skips, and recharges, and by applying memoization, the algorithm efficiently determines if Abhimanyu can cross all circles in different scenarios.

## Contributing

Feel free to open issues or submit pull requests if you have suggestions for improvements or additional test cases.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
