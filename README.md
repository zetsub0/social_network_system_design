# Safar

Homework for course by System Design. Safar is a social network for travelers.

## Functional requirements:
- Create, edit, delete, read posts.
- Like a post.
- Leave a comment below the post.
- Subscription to users.
- Search for interesting places and posts from this places.
- Viewing other travelers profiles.
- Chronologic feed by subscriptions.

## Non-functional requirements:
- 10_000_000 DAU.
- Availability - 99,9%.
- Data is always stored.

- There is seasonality. The growth of users is expected in the vacation season.
- Audience - CIS countries.

- User activity:
 - User creates approximately 0.2 posts per day.
 - User watches travelers page 5 times per day.
 - User watches feed twice per day.
 - User leaves 5 comments per day.
 - User likes 10 posts per day.

- Limits:
  User limit:
    - User cannot create more than 10000 posts.
    - User cannot create more than 100 posts per day.
    - User cannot subscribe to more than 1000 travelers.
  Post limit:
    - Post length less than 1000 symbols.
    - Image size on post less than 3MiB.
    - Maximum 5 photos on post.
  Comment limit:
    - Comment length less than 500 symbols.

- Timings:
  - Post creation - 5s.
  - Feed show - 3s.
  - Profile show - 1s.
  - Send comment - 1s.

## Basic calculations
Create Post RPS:
```
DAU = 10_000_000
DPPU = 0.2 (Daily posts per user)

         DAU * DPPU    10_000_000 * 0.2
RPS = -------------- = ---------------- ≈ 25
           86400            86400
```

Show feed RPS:
```
DAU = 10_000_000
DWF = 2 (Daily watch feed)

         DAU * DWF      10_000_000 * 2
RPS = -------------- = ---------------- ≈ 235
           86400            86400
```

Show travelers profile:
```
DAU = 10_000_000
DWT = 5 (Daily watch travelers profile)

         DAU * DWT      10_000_000 * 5
RPS = -------------- = ---------------- ≈ 580
           86400            86400
```

Leave a comment below the post:
```
DAU = 10_000_000
DC = 5 (Daily comments by user)

         DAU * DC        10_000_000 * 5
RPS = -------------- = ---------------- ≈ 580
           86400            86400
```

Like a post:
```
DAU = 10_000_000
DL = 10 (Daily likes)

         DAU * Dl       10_000_000 * 10
RPS = -------------- = ---------------- ≈ 1160
           86400            86400
```

One post size:
```
Model:
  ID              8b             ˺
  USER_ID         8b             |
  PLACE_ID        8b              > 6.7MiB
  TEXT            ~2kib          |
  IMAGES          ~6MiB          |
  IMAGEs PREVIEW  ~600Kib        ˼
```

One comment size:
```
Model:
  ID        8b        ˺
  POST_ID   8b        |
  AUTHOR_ID 8b         > 1.02 kiB
  CONTENT   ~1kiB     ˼
```

Like size:
```
Model:
  ID        8b        ˺
  POST_ID   8b         > 24b
  AUTHOR_ID 8b        ˼
```


Traffic
```
NEW POST = 6.7 MiB * 25 ≈ 143 MiB/s
TIP: inserting raw images

READ FEED = 602 kiB * 20 * 235 ≈ 2830 kiB/s ≈ 2,78 MiB/s, where 20 is page size
TIP: showing compressed images. An additional analysis is required.

READ PROFILE = 602 kiB * 20 * 580 ≈ 6985 kiB/s ≈ 6.83 MiB/s, where 20 is page size
TIP: showing compressed images. An additional analysis is required.

LEAVE A COMMENT = 1.02 kiB * 580 ≈ 600 kiB/s

LIKE A POST = 24 b * 1160 ≈ 28 kiB/s
```
