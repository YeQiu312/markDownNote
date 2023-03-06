```java
class Solution{
    public List<Interger> preorderTraveral(TreeNode root){
        List<Interger> result = new ArrayList<Interger>();
        preorder(root,result);
        return result
        
    }
    
    
    
    public void preorder(TreeNode root,List<Interger> result){
        if(root == null){
            return;
        }
        
        result.add(root.val);
        preorder.add(root.left, result);
        preorder.add(root.right, result);
    }
    
}
```

