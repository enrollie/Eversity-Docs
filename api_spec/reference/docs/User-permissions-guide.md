# User types and permissions guide

As Eversity originally was a helper app for a Schools.by platform, it inherited most of Schools.by User types.

Right now, Eversity uses those user types:

  - Pupil - pupil of some class, has least of permissions (can only read their class without absence data)
  - Parent - second from bottom from permissions perspective. Can read their pupil's class and access Telegram endpoints
  - Teacher - used by most users. Can read their lesson class (class, they have lesson **on request time**) and modify absence data if it's the first lesson in this class (lesson place equals 0 or 1)
  - Class teacher - inherited from Teacher with one exception - they can modify their class at any time
  - Social Teacher - can read absence data from any class and free to modify any class absence data (note, that it's not inherited from anything).
  - Administration - inherited from Teacher and Class teacher (assuming, they have their own class). Similar to Social teacher, can read absence data from any class, but cannot modify them.
  - System - have highest privileges. Can do literally anything, from modifying Users data to deleting classes. Only supposed to be used by system administrator, as this gives full control over the service. Currently not implemented.

As Eversity uses Oso to control access, if user does not have "read" right on any resource, server will return HTTP Code 404 (Not found). 
In case, where user have "read" right, but cannot issue action (i.e. regular Teacher tries to modify class on sixth lesson), server will return HTTP Code 403 (Forbidden).

Available resources can be determined on client-side using next logic (pseudocode):
```python
is user.type = 'SYSTEM'
  yes: MODIFY ${any}
  no: NOTHING

is user.type = 'ADMINISTRATION'
  yes: 
      is user.classId != null
        yes: MODIFY /class/${user.classId}
        no: NOTHING
      is user.currentLesson != null
        yes: is user.currentLesson.place = 1 or 0
          yes: MODIFY /class/${currentLesson.classId}
          no: READ /class/${currentLesson.classId}
        no: NOTHING
  no: NOTHING

is user.type = 'SOCIAL_TEACHER'
  yes: MODIFY /absence/${any}
  no: NOTHING

is user.type = 'CLASS_TEACHER'
  yes: MODIFY /class/${user.classId}
  no: NOTHING

is user.type = 'TEACHER'
  yes: is user.currentLesson != null
    yes: is user.currentLesson.place = 1 or 0
      yes: MODIFY /class/${currentLesson.classId}
      no: READ /class/${currentLesson.classId}
    no: NOTHING
  no: NOTHING

is user.type = 'PARENT'
  yes: READ_LIMITED /class/${user.pupils.{any}.classId}
       MODIFY /telegram/${user.id}
  no: NOTHING

is user.type = 'PUPIL'
  yes: READ_LIMITED /class/${user.classId}
  no: NOTHING
```