# Frontend Technical Spec

## Pages

### Dashboard '/dashboard.html'

#### Purpose
The purpose of this page is to provide a quick overview of the user's study progress.

#### Components
This page contains the following components:
- Last study session : 
  - shows the last study session
  - shows when last study session was
  - shows the number of words studied
  - shows the number of correct vs incorrect words 
  - has a link to the group that was studied
  
- Study progress :
  - total words study e.g 3/120
    - show the total words studied across all study sessions.
  - display mastery progress
  
- Quick stats :
  - success rate e.g 80%
    - show the percentage of correct words across all study sessions.
  - total study sessions e.g 3
  - total active groups e.g 2
  - study streak e.g 3 days
  
- Start studying button
  - goes to study activities page

#### API Endpoints
We'll need the following API endpoints:
- GET /api/dashboard/last-study-session
- GET /api/dashboard/study-progress
- GET /api/dashboard/quick-stats

### Study Activities '/study-activities.html'

#### Purpose
The purpose of this page is to provide a collection of activities with a thumbnail and its name to either launch or view the study activity.

#### Components
- Study activity card
  - thumbnail
  - name
  - launch button
  - view button 
  - description

#### API Endpoints
- GET /api/study-activities
  - paginated list of study activities

### Study Activities Index '/study-activities/{id}.html'

#### Purpose
The purpose of this page is to provide a detailed view of the study activity.

#### Components
- Study activity details
  - name
  - description
  - thumbnail
  - instructions
  - launch button
  - study activities paginated list
    - id
    - activity name
    - group name
    - start time
    - end time (inferred by the last word review item submitted)
    - number of review items

#### API Endpoints
- GET /api/study-activities/{id}
- GET /api/study-activities/{id}/study-sessions

### Study Activities Launch Index '/study-activities/{id}/launch.html'

#### Purpose
The purpose of this page is to launch the study activity.

#### Components
- Launch form
  - select field for group
  - launch now button

#### Behaviour

After the form is submitted, the user is redirected to the study activity page. 
#### API Endpoints
- POST /api/study-activities/{id}/launch
 
### Words Index '/words.html'

#### Purpose

The purpose of this page is to show all words in the database.

#### Components

- Paginated list of words
  - columns
    - english
    - igbo
    - parts of speech
    - correct count
    - wrong count
    - study sessions
    - review items
  - pagination with 100 items per page
  - clicking the igbo word will take us tot the word show page

#### API Endpoints
- GET /api/words


### Word Show Index '/words/{id}.html'

#### Purpose

The purpose of this page is to show the details of a specific word.

#### Components
- English
- Igbo
- Parts of speech
- Study statistics
  - correct count
  - wrong count
- Word groups
  - show in a series of pills e.g. tags
  - when group name is clicked, it takes us to the group show page
#### API Endpoints
- GET /api/words/{id}


### Word Groups Index '/groups.html'

#### Purpose

The purpose of this page is to show all word groups in the database.

#### Components

- Paginated list of word groups
  - columns
    - group name
    - word count
  Clicking the group name will take us to the group show page
    
#### API Endpoints

- GET /api/groups

### Group Show '/groups/{id}.html'

#### Purpose

The purpose of this page is to show the details of a specific group.

#### Components

- Group name
- Group stats
  - total word count
- Words in group (paginated list of words)
  - should use the same component as the words index page
- Study sessions(paginated list of study sessions)
  - should use the same component as the study sessions index page
  
#### API Endpoints

- GET /api/groups/{id} (the name and group stats)
- GET /api/groups/{id}/words
- GET /api/groups/{id}/study-sessions

### Study Sessions Index '/sessions.html'

#### Purpose

The purpose of this page is to show all study sessions in the database.

#### Components
- Paginated study sessions list
  - columns
    - ID
    - Group name
    - Activity name
    - Start time
    - End time
    - Number of review items
  Clicking the study session ID will take us to the study session show page
  
#### API Endpoints

- GET /api/study-sessions

### Study Session Show '/sessions/{id}.html'

#### Purpose

The purpose of this page is to show the details of a specific study session.

#### Components

- Study session details
  - Activity name
  - Group name
  - Start time
  - End time
  - Number of review items
- Words review items (paginated list of review items)
  - Should use the same component as the words index page
- Back to study activity button
  - goes to the study activity page

#### API Endpoints

- GET /api/study-sessions/{id}
- GET /api/study-sessions/{id}/words

### Settings '/settings.html'

#### Purpose

The purpose of this page is to provide a place for the user to update their settings.

#### Components

- User settings form
  - name
  - email
  - password
  - save button
- Theme settings
  - light mode
  - dark mode
  - system default
- Full reset button
  - resets all progress

#### API Endpoints

- POST /api/settings/reset
- POST /api/settings/full-reset
- POST /api/settings/theme




















------

#### Purpose
#### Components
#### API Endpoints