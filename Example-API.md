# Example API Request & Response Format

## Feed Actions

### Get Feed
- Usage: xxxxxxxx
- Endpoint: api.uclcssa.cn/feed/get

#### Querey Parameters

| Parameters        | Example           | Required  | Remarks                                        |
| ----------------- |:----------------- | :-------- |:----------------------------------------------- |
| userid            | 2453719439374     | Yes       | iFanr ID                                       |
| poststart         | 247               | Yes       |                                                |
| postend           | 219               | Yes       |                                                |
| space             | 2  (E.g. Accom)   | No        | Specify to get post only in that space         |



#### Example response

```
{
    status: 'OK',
    posts: [{
        avatar: "https://avatar.url",
        comments: [{user_id:..., content:..., time:..., user:..., coment_id:...}],
        content: "post content",
        images: [...],
        liked: false,
        likes: 0,
        post_id: 350,
        spacename: "房屋出租",
        time: "...",
        user: "username",
        user_id: "xxxx"
    },
    ...
    ]
}
```
