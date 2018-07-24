# json类型
| id | content |
| - | - |
| 1 | {"id": 1, "name": "filename", "type": "file", "value": "hello world!"} |

- 查询

```sql
SELECT content, content->"$.id"
FROM table_name
WHERE content->"$.id" > 1
ORDER BY content->"$.type";
```

- 删除

```sql
DELETE FROM table_name WHERE content->"$.id" = 1;
```
