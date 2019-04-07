
## Algorithm

### Description

Given a binary tree, return the bottom-up level order traversal of its nodes' values. (ie, from left to right, level by level from leaf to root).

For example:
Given binary tree [3,9,20,null,null,15,7],
```
    3
   / \
  9  20
    /  \
   15   7
```
return its bottom-up level order traversal as:
```
[
  [15,7],
  [9,20],
  [3]
]
```


### Solution

```java

import java.util.LinkedList;
import java.util.List;
import java.util.Queue;
import java.util.concurrent.LinkedBlockingQueue;

public class BinaryTreeLevelOrderTraversalII {
    public List<List<Integer>> levelOrderBottom(TreeNode root) {
        if (root == null) return null;

        Queue<WrappedTreeNode> queue = new LinkedBlockingQueue<>();
        int currLevel = 0;
        queue.add(new WrappedTreeNode(0, root));

        List<List<Integer>> result = new LinkedList<>();
        result.add(new LinkedList<>());
        while (!queue.isEmpty()) {
            WrappedTreeNode node = queue.remove();
            if (currLevel == node.level) {
                result.get(0).add(node.node.val);
            } else {
                result.add(0,new LinkedList<>());
                result.get(0).add(node.node.val);
                currLevel++;
            }
            if (node.node.left != null) {
                queue.add(new WrappedTreeNode(node.level +1, node.node.left));
            }
            if (node.node.right != null) {
                queue.add(new WrappedTreeNode(node.level + 1, node.node.right));
            }
        }

        return result;

    }

    static class WrappedTreeNode {
        int level;
        TreeNode node;

        WrappedTreeNode(int level, TreeNode node) {
            this.level = level;
            this.node = node;
        }
    }
}
```

## Review 

[A Befuddled Adult’s Guide to Playing With Little Kids
](https://medium.com/s/story/a-befuddled-adults-guide-to-playing-with-little-kids-d16043be535e)
- To children, play is innate— an instinctive, evolutionary behavior—and the boundary between play and reality doesn’t exist. When a child is a dragon, they are a dragon. When you’re the dragon? You’re acting.
- New borns are better to process faces -> speed time with child & choose storytime books with drawings and characters
- Voice is also important for newborns. using baby talk & vocalize what you're doing & narrate events around house -> help build love and trust -> build baby's language skills
- Wait for an invitation into the child’s world. Follow their verbal and nonverbal cues
- If you’re invited into the play but you’re not quite sure how to engage, try mirroring the child.
- Your job is to keep children from killing themselves, not from hurting themselves.
- you also want them to remember that play involves compromise, especially when they’re doing it with other kids. 


## Tips

[关于在Spring 中方法内部调用自身方法事务 REQUIRE_NEW 不生效的解释](https://my.oschina.net/liufq/blog/3033055)

## Share

[MVC VS MVVM](https//my.oschina.net/liufq/blog/3032850)
