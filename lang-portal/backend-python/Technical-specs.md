# Backend Server Technical specs

## Business Goal

A language learning school wants to build a prototype of learning portal which will act as three things:
- Inventory of possible vocabulary that can be learned
- Act as a  Learning record store (LRS), providing correct and wrong score on practice vocabulary
- A unified launchpad to launch different learning apps

## Technical Requirements

- The backend server will be built using Python and Flask. 
- The database will be SQLite3.
- The API will be built using Django Rest Framework.
- The API will always return a JSON response.

## Directory Structure

```text
backend-python/
├── app/
│   ├── __init__.py
│   ├── models/
│   │   ├── __init__.py
│   │   ├── word.py
│   │   ├── group.py
│   │   ├── study_session.py
│   │   ├── study_activity.py
│   │   └── word_review_item.py
│   ├── routes/
│   │   ├── __init__.py
│   │   ├── dashboard.py
│   │   ├── words.py
│   │   ├── groups.py
│   │   ├── study_sessions.py
│   │   ├── study_activities.py
│   │   └── settings.py
│   └── services/
│       ├── __init__.py
│       ├── database.py
│       └── stats.py
├── db/
│   ├── migrations/
│   │   ├── 001-initial.sql
│   │   └── 002-add-words-table.sql
│   └── seeds/
│       ├── basic_greetings.json
│       └── common_phrases.json
├── tests/
│   ├── __init__.py
│   ├── test_models.py
│   └── test_routes.py
├── words.db
├── config.py
├── requirements.txt
└── tasks.py
```

## Database Schema

Our database will be a SQLite3 database called `words.db` that will be th root of the project folder of `backend-python`.

- The database will have two tables: 
    - words : stored vocabulary words
      - id : primary key (integer)
      - english (string)
      - igbo (string)
      - parts : json structure specifying the parts of speech of the word (json)
  
    - word_groups : join table between words and groups. 
    many-to-many relationship
      - id
      - word_id (integer)
      - group_id (integer)
      
    - groups : thematic grouping of words
      - id : primary key (integer)
      - name : name of the group (string)
      
    - study_sessions : join table between groups and study_activities
      - id : primary key (integer)
      - group_id : foreign key to groups (integer)
      - study_activity_id : foreign key to study_activities (integer)
  
    - study_activities : a specific study activity for a group
      - name : name of the activity (string)
      - id : primary key (integer)
      - url : url of the activity (string)
      
    - word_review_items : join table between words and study_sessions
      - id : primary key (integer)
      - word_id : foreign key to words (integer)
      - study_session_id : foreign key to study_sessions (integer)
      - correct : boolean

### API Endpoints:

### GET /api/dashboard/last-study-session

Returns information about the most recent study session.

#### JSON Response:
```json
{
  "id": 1,
  "created_at": "2024-03-20T15:30:00Z",
  "study_activity_id": 1,
  "group_id": 1,
  "group_name": "Basic Greetings"
}
```

### GET /api/dashboard/study-progress
Returns study progress statistics.
Please note that the frontend will determine progress bar based on total words studied.

#### JSON Response:
```json
{
  "total_words": 120,
  "studied_words": 45
}
```

### GET /api/dashboard/quick-stats

#### JSON Response:
Returns quick overview stats.

```json
{
  "success_rate": 80,
  "total_study_sessions": 3,
  "total_active_groups": 2,
  "study_streak": 3
}

```

### GET /api/study-activities/{id}

#### JSON Response:
```json
{
  "id": 1,
  "name": "Vocabulary Quiz",
  "description": "Practice your vocabulary with multiple choice questions",
  "thumbnail": "/images/vocab-quiz.jpg",
  "instructions": "Select the correct translation for each word",
  "url": "https://example.com/activities/vocab-quiz"
}
```

### GET /api/study-activities/{id}/study-sessions
- pagination with 100 items per page

#### JSON Response:
```json
{
  "items": [
    {
      "id": 1,
      "activity_name": "Vocabulary Quiz",
      "group_name": "Basic Greetings",
      "start_time": "2024-03-20T15:30:00Z",
      "end_time": "2024-03-20T15:45:00Z",
      "review_items_count": 15
    }
  ],
  "pagination": {
    "current_page": 1,
    "total_pages": 5,
    "total_items": 48,
    "items_per_page": 10
  }
}
```

### POST /api/study-activities
#### Request Params
- group_id integer 
- study_activity_id integer

#### JSON Response:
```json
{
  "id": 1,
  "group_id": 12
}
```

### GET /api/words

- pagination with 100 items per page

#### JSON Response

```json
{
  "items": [
    {
      "english": "hello",
      "igbo": "ndewo",
      "parts_of_speech": ["noun", "interjection"],
      "correct_count": 25,
      "wrong_count": 5
    }
  ],
  "pagination": {
    "current_page": 1,
    "total_pages": 10,
    "total_items": 1000,
    "items_per_page": 100
  }
}
```

### GET /api/words/{id}
#### JSON Response
```json
{
    "english": "hello",
    "stats": {
      "correct_count": 25,
      "wrong_count": 5,
      "study_sessions_count": 3,
      "review_items_count": 30
    },
    "groups": [
      {
        "id": 1,
        "name": "Basic Greetings"
      }
    ]
}

```

### GET /api/groups
- pagination with 100 items per page
#### JSON Response
```json
{
  "items": [
    {
      "id": 1,
      "name": "Basic Greetings",
      "word_count": 15
    }
  ],
  "pagination": {
    "current_page": 1,
    "total_pages": 10,
    "total_items": 1000,
    "items_per_page": 100
  }
}
```


### GET /api/groups/{id} (the name and group stats)
#### JSON Response
```json
{
  "id": 1,
  "name": "Basic Greetings",
  "stats": {
    "word_count": 15,
    "study_sessions_count": 3,
    "success_rate": 80
  }
}
```


### GET /api/groups/{id}/words
#### JSON Response
```json
{
  "items": [
    {
      "id": 1,
      "english": "hello",
      "igbo": "ndewo",
      "parts_of_speech": ["noun", "interjection"],
      "correct_count": 25,
      "wrong_count": 5
    }
  ],
  "pagination": {
    "current_page": 1,
    "total_pages": 10,
    "total_items": 1000,
    "items_per_page": 100
  }
}
```


### GET /api/study-sessions
#### JSON Response
```json
{
  "items": [
    {
      "id": 1,
      "activity_name": "Vocabulary Quiz",
      "group_name": "Basic Greetings",
      "start_time": "2024-03-20T15:30:00Z",
      "end_time": "2024-03-20T15:45:00Z",
      "review_items_count": 15
    }
  ],
  "pagination": {
    "current_page": 1,
    "total_pages": 5,
    "total_items": 48,
    "items_per_page": 100
  }
}
```


### GET /api/groups/{id}/study-sessions
#### JSON Response
```json
{
  "items": [
    {
      "id": 1,
      "activity_name": "Vocabulary Quiz",
      "group_name": "Basic Greetings",
      "start_time": "2024-03-20T15:30:00Z",
      "end_time": "2024-03-20T15:45:00Z",
      "review_items_count": 15
    }
  ],
  "pagination": {
    "current_page": 1,
    "total_pages": 5,
    "total_items": 48,
    "items_per_page": 100
  }
}
```


### GET /api/study-sessions/{id}
#### JSON Response
```json
{
  "id": 1,
  "activity_name": "Vocabulary Quiz",
  "group_name": "Basic Greetings",
  "start_time": "2024-03-20T15:30:00Z",
  "end_time": "2024-03-20T15:45:00Z",
  "review_items_count": 15
}
```


### GET /api/study-sessions/{id}/words
#### JSON Response
```json
{
  "items": [
    {
      "id": 1,
      "english": "hello",
      "igbo": "ndewo",
      "parts_of_speech": ["noun", "interjection"],
      "correct_count": 25,
      "wrong_count": 5
    }
  ],
  "pagination": {
    "current_page": 1,
    "total_pages": 10,
    "total_items": 1000,
    "items_per_page": 100
  }
}
```

### POST /api/settings/reset
#### JSON Response
```json
{
  "success": true,
  "message": "Study history reset successfully"
}
```

### POST /api/settings/full-reset
#### JSON Response
```json
{
  "success": true,
  "message": " Full user data and settings reset successfully"
}
```

### POST /api/study-sessions/{id}/words/{word-id}/review

#### Request Params
- study_session_id (integer)
- word_id (integer)
- correct (boolean)
- 
#### Request Payload
```json
{
  "correct": true
}
```

#### JSON Response
```json
{
  "success": true,
  "word_id": 123,
  "study_session_id": 456,
  "correct": true,
  "created_at": "2024-03-20T15:30:00Z"
}
```

### POST /api/settings/theme

#### Request Params
- theme string
  - light string
  - dark string
  - system default string

#### Request Payload
```json
{
  "theme": "dark"
}
```

#### JSON Response
```json
{
  "success": true,
  "theme": "light",
  "message": "Theme updated successfully"
}
```
## Invoke Tasks

Invoke is a task runner for Python.
Let's list out possible tasks we need for lang portal.

### Initialize Database
This task will initialize the sqlite database called `words.db` .

### Migrate Database
This task will run a series of migration sql files on the database.

Migration files live in the `migrations` directory.
The migration files should run in order of their filenames.
The file name should look like this 
```sql
`001-initial.sql`, 
`002-add-words-table.sql`, 
```

### Seed Database
This task will import json files and transfers them into target data for our database.

All seed files live in the `seeds` directory.
In our task we should have DSL to specify each seed file and its expected group word name
```json
[
  {
    "igbo": "oma",
    "english": "good"
  }
] 
```
