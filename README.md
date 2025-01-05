DYNAMIC PROGRAMMING
  1) Leetcode 494 - Target SUm
  2) Leetcode 2381

     The Fenwick Tree will efficiently handle range updates (shifts) and prefix sum queries.

     class FenwickTree {
    private int[] fenwickTree;
    private int size;

    // Constructor
    public FenwickTree(int n) {
        size = n;
        fenwickTree = new int[n + 1]; // 1-based indexing
    }

    // Update: Add `delta` to the range [i, j]
    public void updateRange(int i, int j, int delta) {
        update(i, delta);
        update(j + 1, -delta);
    }

    // Update: Add `delta` to index i
    private void update(int i, int delta) {
        while (i <= size) {
            fenwickTree[i] += delta;
            i += i & -i;
        }
    }

    // Query: Get the prefix sum up to index i
    public int query(int i) {
        int sum = 0;
        while (i > 0) {
            sum += fenwickTree[i];
            i -= i & -i;
        }
        return sum;
    }
}

class Solution {
    public String shiftingLetters(String s, int[][] shifts) {
        int n = s.length();
        FenwickTree fenwickTree = new FenwickTree(n);

        // Apply shifts using the Fenwick Tree
        for (int[] shift : shifts) {
            int start = shift[0] + 1; // Convert to 1-based index
            int end = shift[1] + 1;   // Convert to 1-based index
            int direction = shift[2] == 1 ? 1 : -1;
            fenwickTree.updateRange(start, end, direction);
        }

        // Build the result string
        StringBuilder sb = new StringBuilder(s);
        for (int i = 0; i < n; i++) {
            int shift = fenwickTree.query(i + 1) % 26; // Convert to 1-based index
            char newChar = (char) ((sb.charAt(i) - 'a' + shift + 26) % 26 + 'a'); // Handle wrapping
            sb.setCharAt(i, newChar);
        }

        return sb.toString();
    }
}



The Segment Tree will efficiently handle range updates and point queries. Here's the implementation:

class SegmentTree {
    private int[] tree;
    private int size;

    // Constructor
    public SegmentTree(int n) {
        size = n;
        tree = new int[4 * n]; // Segment tree size
    }

    // Update: Add `delta` to the range [l, r]
    public void updateRange(int node, int start, int end, int l, int r, int delta) {
        if (start > r || end < l) {
            return; // No overlap
        }

        if (start >= l && end <= r) {
            tree[node] += delta; // Complete overlap
            return;
        }

        // Partial overlap
        int mid = start + (end - start) / 2;
        updateRange(2 * node + 1, start, mid, l, r, delta);
        updateRange(2 * node + 2, mid + 1, end, l, r, delta);
    }

    // Query: Get the cumulative value at index `idx`
    public int query(int node, int start, int end, int idx, int carry) {
        if (start == end) {
            return tree[node] + carry; // Leaf node
        }

        int mid = start + (end - start) / 2;
        if (idx <= mid) {
            return query(2 * node + 1, start, mid, idx, carry + tree[node]);
        } else {
            return query(2 * node + 2, mid + 1, end, idx, carry + tree[node]);
        }
    }
}

class Solution {
    public String shiftingLetters(String s, int[][] shifts) {
        int n = s.length();
        SegmentTree segmentTree = new SegmentTree(n);

        // Apply shifts using the Segment Tree
        for (int[] shift : shifts) {
            int start = shift[0];
            int end = shift[1];
            int direction = shift[2] == 1 ? 1 : -1;
            segmentTree.updateRange(0, 0, n - 1, start, end, direction);
        }

        // Build the result string
        StringBuilder sb = new StringBuilder(s);
        for (int i = 0; i < n; i++) {
            int shift = segmentTree.query(0, 0, n - 1, i, 0) % 26;
            char newChar = (char) ((sb.charAt(i) - 'a' + shift + 26) % 26 + 'a'); // Handle wrapping
            sb.setCharAt(i, newChar);
        }

        return sb.toString();
    }
}


