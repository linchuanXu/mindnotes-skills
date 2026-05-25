# Purchases Reference

Use this when the user asks about purchases, assets, 物卡, item value, retirement, selling price, or personal asset records.

## Read

List purchase records:

```json
{"api_name":"/purchases/list","category":"数码","count":50,"skill_version":"1.0.0"}
```

List categories:

```json
{"api_name":"/purchases/categories","skill_version":"1.0.0"}
```

## Write

Create only when the user asks to record an item:

```json
{"api_name":"/purchases/create","name":"显示器","value":1200,"category":"数码","description":"4K 屏幕","image_url":"static/uploads/7-alice/cover.png","skill_version":"1.0.0"}
```

Update a resolved record:

```json
{"api_name":"/purchases/update","purchase_id":1,"condition":"良好","selling_price":800,"skill_version":"1.0.0"}
```

Delete only after explicit confirmation:

```json
{"api_name":"/purchases/delete","purchase_id":1,"confirm_delete":true,"skill_version":"1.0.0"}
```

Use `/assets/upload-image` first when the user provides an image. Store the returned `asset.path` as `image_url`.

Fields: `name`, `value`, `category`, `description`, `condition`, `purchase_date`, `image_url`, `retirement_date`, `selling_price`.

Reads require `purchases:read`; writes require `purchases:write`.
