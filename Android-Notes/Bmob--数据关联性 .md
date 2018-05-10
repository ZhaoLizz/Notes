# 数据关联性

- pointer：一对一、一对多 relative：多对多

- 创建数据对象

```java
public class MyUser extends BmobUser {
    private Integer age;

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }
}

//-------------------
//帖子
public class Post extends BmobObject {
    private String title;
    private String content;
    private MyUser author;
    private BmobFile image;
    private BmobRelation likes; //多对多关系，用于存储喜欢该帖子的用户
    getter、setter

//评论-----------------------
public class Comment extends BmobObject {
    private String content;
    private MyUser user;
    private Post post;
    getter、setter
```

## 一、一对一关系

1. 添加一对一关联

```java
//注册、登陆用户
...

//必须获取到当前用户。如果仍然用上文注册登陆时new的user添加一对一关系会出现null关联。
MyUser currentUser = BmobUser.getCurrentUser(MyUser.class);

        //创建帖子信息
        Post post = new Post();
        post.setContent("abcdefg");

        //添加一对一关联
        post.setAuthor(currentUser);
        post.save(new SaveListener<String>() {
            @Override
            public void done(String s, BmobException e) {
                if (e == null) {
                    Log.d("main", "save succeed");
                } else {
                    Log.d("main", "save failed" + e.getMessage());
                }
            }
        });
```

1. 查询一对一关系

```java

//查询当前用户(user)的所有帖子(Post)
MyUser currentUser = BmobUser.getCurrentUser(MyUser.class);
        BmobQuery<Post> query = new BmobQuery<>();
        query.addWhereEqualTo("author", currentUser);//查询当前用户(user)的所有帖子(Post)
        query.order("-updatedAt");
        query.include("author");//在查询帖子信息的同时查询发布人的信息
        query.findObjects(new FindListener<Post>() {
            @Override
            public void done(List<Post> list, BmobException e) {
                if (e == null) {
                    Log.d(TAG, "done: query succeed "+ list.size());
                    for (Post post :
                            list) {
                        textView.setText(post.getContent() + "\n" + post.getAuthor().get...);
                    }
                } else {
                    Log.d(TAG, "done: query failed" + e.getMessage());
                }
            }
        });
```

1. 更新一对一关联

```java
//将帖子的作者改为用户B
        Post p = new Post();

        //构造用户B，如果已知pojectId，可以setobjectId，否则需要将
        //用户B查询出来
        MyUser userB = new MyUser();
        userB.setObjectId("72961f79ae");//DaMing

        p.setAuthor(userB);
        p.update(new UpdateListener() {
            @Override
            public void done(BmobException e) {
                if (e == null) {
                    Log.d(TAG, "done: updata succeed");
                } else {
                    Log.d(TAG, "done: updata failed:"+e.getMessage());
                }
            }
        });
```

1. 删除一对一关联

```java
Post post = new Post();
        post.remove("author");
        post.update("06e7ef0c5a", new UpdateListener() {
            @Override
            public void done(BmobException e) {
                if (e == null) {
                    Log.d(TAG, "done: delete succeed");
                } else {
                    Log.d(TAG, "done: delete failed" + e.getMessage());
                }
            }
        });
```

## 一对多关联

1. 添加一对多关联

```java
//和一对一关联一样
Post post = new Post();
        post.setObjectId("06e7ef0c5a");
        final Comment comment = new Comment();
        comment.setContent("hello");
        comment.setPost(post);
        comment.setUser(currentUser);
        comment.save(new SaveListener<String>() {
            @Override
            public void done(String s, BmobException e) {
                if (e == null) {
                    Log.d(TAG, "done: comment succeed");
                } else {
                    Log.d(TAG, "done: comment failed"+e.getMessage());
                }
            }
        });
```

1. 查询一对多关联
