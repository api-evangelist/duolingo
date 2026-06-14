# Duolingo GraphQL Schema

## Overview

This directory contains a conceptual GraphQL schema for Duolingo, the world's most-downloaded language-learning app. Duolingo does not publish an official public GraphQL or REST API for third-party developers; the schema here is derived from the community-documented reverse-engineered API surface at [duolingo-api.readthedocs.io](https://duolingo-api.readthedocs.io/) and observation of the unofficial `https://www.duolingo.com/api/1` endpoints.

The schema models the full breadth of Duolingo's gamified learning platform: users, streaks, XP, courses, skill trees, lessons, challenges, vocabulary, social features (friends, clubs, leaderboards), achievements, trophies, placement tests, and the Duolingo English Test certificate surface.

## Schema File

- [`duolingo-schema.graphql`](duolingo-schema.graphql) — Conceptual GraphQL SDL with 60+ named types.

## Type Summary

| Category | Types |
|---|---|
| User | `User`, `UserDetails`, `UserStats`, `UserStreak` |
| Language & Course | `Language`, `LanguageDetails`, `LanguageCode`, `Course`, `CourseDetails` |
| Skill & Tree | `Skill`, `SkillDetails`, `SkillTree` |
| Unit & Level | `Unit`, `UnitDetails`, `Level`, `LevelDetails` |
| Lesson | `Lesson`, `LessonDetails` |
| Challenge & Exercise | `Challenge`, `ChallengeType`, `Exercise` |
| Vocabulary & Words | `Vocabulary`, `Word`, `WordDetails`, `WordTranslation`, `WordProficiency` |
| Gamification | `XP`, `XPDetails`, `StreakDetails`, `StreakFreezes`, `Gems`, `Lingots`, `Health` |
| Goals & Practice | `DailyGoal`, `DailyGoalProgress`, `Practice` |
| Social | `LeaderBoard`, `LeaderBoardEntry`, `Friend`, `FriendDetails`, `FriendActivity` |
| Achievements | `Achievement`, `AchievementDetails`, `Trophy`, `Certificate` |
| Placement & Profile | `Placement`, `Avatar` |
| Clubs | `Club`, `ClubDetails` |
| Auth & API | `APIKey`, `Token` |
| Pagination | `PageInfo`, `UserConnection`, `UserEdge`, `WordConnection`, `WordEdge` |
| Enums | `LanguageCode`, `ChallengeType`, `SkillLevel`, `ProficiencyLevel`, `GoalType`, `LeaderboardTimeframe`, `AchievementCategory` |

## Key Queries

```graphql
# Fetch a user with their streak and XP
query GetUser($id: ID!) {
  user(id: $id) {
    id
    username
    streak {
      currentStreak
      longestStreak
      isStreakActive
    }
    xp {
      totalXP
      weeklyXP
      level
    }
  }
}

# Fetch the skill tree for a course
query GetSkillTree($courseId: ID!) {
  skillTree(courseId: $courseId) {
    skills {
      id
      name
      level
      progress
      crowns
      isAccessible
    }
  }
}

# Fetch vocabulary for a user in a language
query GetVocabulary($userId: ID!, $languageCode: LanguageCode!) {
  vocabulary(userId: $userId, languageCode: $languageCode) {
    totalWords
    masteredWords
    dueForReview {
      word
      proficiency {
        strength
        level
        nextReviewAt
      }
    }
  }
}

# Fetch the weekly leaderboard
query GetLeaderboard {
  leaderboard(timeframe: WEEKLY) {
    entries {
      rank
      user { username }
      xp
    }
    userRank
    totalParticipants
  }
}
```

## Key Mutations

```graphql
# Start a lesson
mutation StartLesson($skillId: ID!) {
  startLesson(skillId: $skillId) {
    id
    challenges {
      id
      type
      prompt
    }
    xpReward
  }
}

# Select a new course
mutation SelectCourse($from: LanguageCode!, $learning: LanguageCode!) {
  selectCourse(fromLanguage: $from, learningLanguage: $learning) {
    id
    title
    skillTree { totalSkills completedSkills }
  }
}
```

## Notes

- Duolingo does not provide an official public API or GraphQL endpoint for third-party developers. The schema here is conceptual, modeled from the community reverse-engineered REST surface.
- The Duolingo English Test (DET) for institutions uses a private API-key program negotiated directly with accepting institutions.
- Community clients targeting `https://www.duolingo.com/api/1` are not officially supported and may break without notice.
- Language codes in this schema follow ISO 639-1 conventions as used by the Duolingo platform.

## References

- [Duolingo API (community docs)](https://duolingo-api.readthedocs.io/)
- [Duolingo GitHub](https://github.com/duolingo)
- [Duolingo English Test](https://englishtest.duolingo.com)
- [Duolingo for Schools](https://schools.duolingo.com)
