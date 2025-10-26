## 📖 API Documentation

### **Base URL**
`https://modlima.fuadfakhruz.id`

### **Response Format**

#### Success Response:

```json
{
  "success": true,
  "message": "Operation successful",
  "data": { ... }
}
```

#### Error Response:

```json
{
  "success": false,
  "message": "Error description",
  "error": "Detailed error message"
}
```

#### Paginated Response:

```json
{
  "success": true,
  "data": [ ... ],
  "pagination": {
    "page": 1,
    "limit": 10,
    "total": 100,
    "total_pages": 10
  }
}
```

### **Endpoints**

#### **🍲 Recipes**

##### `GET /api/v1/recipes`

Get all recipes dengan pagination, search, dan filter.

**Query Parameters:**

- `page` (int) - Page number (default: 1)
- `limit` (int) - Items per page (default: 10)
- `category` (string) - Filter by category: `makanan` | `minuman`
- `difficulty` (string) - Filter by difficulty: `mudah` | `sedang` | `sulit`
- `search` (string) - Search in name/description
- `sort_by` (string) - Sort by field (default: `created_at`)
- `order` (string) - Sort order: `asc` | `desc` (default: `desc`)

**Example:**

```bash
GET /api/v1/recipes?category=makanan&difficulty=mudah&page=1&limit=10
```

##### `GET /api/v1/recipes/:id`

Get recipe by ID.

**Response:**

```json
{
  "success": true,
  "message": "Recipe retrieved successfully",
  "data": {
    "id": "uuid",
    "name": "Nasi Goreng",
    "category": "makanan",
    "description": "...",
    "image_url": "https://cdn.modlima.fuadfakhruz.id/xxx.jpg",
    "prep_time": 15,
    "cook_time": 20,
    "servings": 4,
    "difficulty": "mudah",
    "is_featured": true,
    "average_rating": 4.5,
    "review_count": 10,
    "ingredients": [
      {
        "id": "uuid",
        "name": "Nasi putih",
        "quantity": "500 gram"
      }
    ],
    "steps": [
      {
        "id": "uuid",
        "step_number": 1,
        "instruction": "Panaskan minyak..."
      }
    ],
    "created_at": "2024-01-01T00:00:00Z",
    "updated_at": "2024-01-01T00:00:00Z"
  }
}
```

##### `POST /api/v1/recipes`

Create new recipe.

**Request Body:**

```json
{
  "name": "Nasi Goreng Spesial",
  "category": "makanan",
  "description": "Nasi goreng dengan bumbu khas nusantara",
  "image_url": "https://cdn.modlima.fuadfakhruz.id/xxx.jpg",
  "prep_time": 15,
  "cook_time": 20,
  "servings": 4,
  "difficulty": "mudah",
  "is_featured": false,
  "ingredients": [
    {
      "name": "Nasi putih",
      "quantity": "500 gram"
    }
  ],
  "steps": ["Panaskan minyak di wajan", "Tumis bumbu halus hingga harum"]
}
```

**Validation:**

- `name` - required, min 3, max 200 chars
- `category` - required, must be `makanan` or `minuman`
- `ingredients` - required, min 1 item
- `steps` - required, min 1 item

##### `PUT /api/v1/recipes/:id`

Update existing recipe (full replacement). All fields are required, same as POST.

**Note:** This replaces the entire recipe including all ingredients and steps.

##### `PATCH /api/v1/recipes/:id`

Partially update recipe. Only send the fields you want to update.

**Request Body Examples:**

Update name only:

```json
{
  "name": "Nasi Goreng Special Updated"
}
```

Update image URL only:

```json
{
  "image_url": "https://cdn.modlima.fuadfakhruz.id/new-image.jpg"
}
```

Update multiple fields:

```json
{
  "name": "Nasi Goreng Special",
  "difficulty": "sedang",
  "prep_time": 20,
  "is_featured": true
}
```

Update ingredients only:

```json
{
  "ingredients": [
    {
      "name": "Nasi putih",
      "quantity": "600 gram"
    },
    {
      "name": "Telur",
      "quantity": "2 butir"
    }
  ]
}
```

**Available Fields:**

- `name` (string, min 3, max 200 chars)
- `category` (string, `makanan` or `minuman`)
- `description` (string)
- `image_url` (string)
- `prep_time` (int, >= 0)
- `cook_time` (int, >= 0)
- `servings` (int, >= 1)
- `difficulty` (string, `mudah`, `sedang`, or `sulit`)
- `is_featured` (boolean)
- `ingredients` (array, min 1 if provided)
- `steps` (array of strings, min 1 if provided)

**Note:**

- Only provided fields will be updated
- If `ingredients` or `steps` are provided, they will completely replace the old ones
- Omitted fields will keep their current values

##### `DELETE /api/v1/recipes/:id`

Delete recipe (cascade delete ingredients, steps, reviews, favorites).

---

#### **⭐ Reviews**

##### `GET /api/v1/recipes/:recipe_id/reviews`

Get all reviews for a recipe.

**Response:**

```json
{
  "success": true,
  "message": "Reviews retrieved successfully",
  "data": [
    {
      "id": "uuid",
      "recipe_id": "uuid",
      "user_identifier": "user123",
      "rating": 5,
      "comment": "Enak banget!",
      "created_at": "2024-01-01T00:00:00Z",
      "updated_at": "2024-01-01T00:00:00Z"
    }
  ]
}
```

##### `POST /api/v1/recipes/:recipe_id/reviews`

Create review for a recipe.

**Request Body:**

```json
{
  "user_identifier": "user123",
  "rating": 5,
  "comment": "Resep yang sangat enak dan mudah dibuat!"
}
```

**Validation:**

- `user_identifier` - required, min 1 char
- `rating` - required, must be 1-5
- `comment` - optional, max 500 chars

##### `PUT /api/v1/reviews/:id`

Update existing review.

**Request Body:**

```json
{
  "rating": 4,
  "comment": "Updated review comment"
}
```

**Validation:**

- `rating` - required, must be 1-5
- `comment` - optional, max 500 chars

##### `DELETE /api/v1/reviews/:id`

Delete review.

---

#### **❤️ Favorites**

##### `GET /api/v1/favorites`

Get all favorite recipes by user identifier.

**Query Parameters:**

- `user_identifier` (string) - required

**Example:**

```bash
GET /api/v1/favorites?user_identifier=user123
```

**Response:**

```json
{
  "success": true,
  "message": "Favorites retrieved successfully",
  "data": [
    {
      "id": "uuid",
      "name": "Nasi Goreng",
      "category": "makanan",
      "description": "...",
      "image_url": "https://cdn.modlima.fuadfakhruz.id/xxx.jpg",
      "prep_time": 15,
      "cook_time": 20,
      "servings": 4,
      "difficulty": "mudah",
      "is_featured": true,
      "average_rating": 4.5,
      "review_count": 10,
      "created_at": "2024-01-01T00:00:00Z",
      "updated_at": "2024-01-01T00:00:00Z"
    }
  ]
}
```

##### `POST /api/v1/favorites/toggle`

Toggle favorite (add if not exists, remove if exists).

**Request Body:**

```json
{
  "recipe_id": "uuid",
  "user_identifier": "user123"
}
```

**Response:**

```json
{
  "success": true,
  "message": "Recipe added to favorites",
  "data": {
    "is_favorited": true
  }
}
```

---

#### **📸 Upload**

##### `POST /api/v1/upload`

Upload recipe image to MinIO.

**Request:**

- Content-Type: `multipart/form-data`
- Field: `image` (file)

**Example (curl):**

```bash
curl -X POST http://localhost:18080/api/v1/upload \
  -F "image=@photo.jpg"
```

**Response:**

```json
{
  "success": true,
  "message": "Image uploaded successfully",
  "data": {
    "url": "https://cdn.modlima.fuadfakhruz.id/xxx-xxx-xxx.jpg",
    "filename": "xxx-xxx-xxx.jpg"
  }
}
```

**Validation:**

- Max file size: 5 MB
- Allowed extensions: `.jpg`, `.jpeg`, `.png`, `.webp`

---
