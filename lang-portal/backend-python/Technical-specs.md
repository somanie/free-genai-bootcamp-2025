# Backend Server Technical specs

## Business Goal: 

A language learning school wants to build a prototype of learning portal which will act as three things:
- Inventory of possible vocabulary that can be learned
- Act as a  Learning record store (LRS), providing correct and wrong score on practice vocabulary
- A unified launchpad to launch different learning apps

## Technical Requirements:

- The backend server will be built using Python and Flask. 
- The database will be SQLite3.
- The API will be built using Django Rest Framework.
- The API will always return a JSON response.

## Database Schema:
- The database will have two tables: 
    - words : stored vocabulary words
      - id : primary key (integer)
      - english (string)
      - igbo (string)
      - parts : json structure specifiying the parts of speech of the word (json)
  
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
- GET /api/dashboard/last-study-session
- GET /api/dashboard/study-progress
- GET /api/dashboard/quick-stats
- GET /api/study-activities/{id}
- GET /api/study-activities/{id}/study-sessions
- POST /api/study-activities/{id}/launch
  - required parameters: group_id, study_activity_id
- GET /api/words
  - pagination with 100 items per page
- GET /api/words/{id}
- GET /api/groups
  - pagination with 100 items per page
- GET /api/groups/{id} (the name and group stats)
- GET /api/groups/{id}/words
- GET /api/study-sessions
- GET /api/groups/{id}/study-sessions
- GET /api/study-sessions/{id}
- GET /api/study-sessions/{id}/words
- POST /api/study-sessions/{id}/words/{word-id}/review
  - required parameters: correct (boolean)
- POST /api/settings/reset
- POST /api/settings/full-reset
- POST /api/settings/theme


