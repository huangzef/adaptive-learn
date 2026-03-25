# adaptive-learn
# Adaptive Learning Skills System

An AI-powered adaptive learning system that guides learners from vague goals to completed projects through a structured, personalized journey.

## Overview

This system provides a complete learning experience orchestrated by multiple specialized AI skills. It transforms your learning intention into a structured path, guides you through knowledge acquisition, and helps you complete real projects.

```
User says "I want to learn X"
        ↓
[CLARIFICATION] Goal Clarifier - Refine your goals
        ↓
[DECOMPOSITION] Goal Decomposer - Generate learning path
        ↓
[TEACHING] Mentor Guide / Socratic Tutor - Learn with guidance
        ↓
[PROJECT] Project Tracker - Build real projects
        ↓
[COMPLETED] You've mastered it!
```

## Core Features

- **State Machine Driven**: Clear progression through learning stages
- **Continuous Scoring System**: Real-time adaptation based on your performance
- **Parameter-Driven Teaching**: Granularity, practice density, and check frequency adjust dynamically
- **File-Based Persistence**: All progress saved in Markdown files
- **Multi-Skill Collaboration**: Specialized skills work together seamlessly

## Skills Architecture

| Skill | Role | Trigger |
|:------|:-----|:--------|
| **learning-session-manager** | Core orchestrator | "I want to learn X" |
| **goal-clarifier** | Goal refinement | "Help me clarify my goal" |
| **goal-decomposer** | Path generation | "Generate learning path" |
| **mentor-guide** | Structured teaching | "Start teaching" |
| **socratic-tutor** | AI companion learning | "I want an AI teacher" |
| **project-tracker** | Project execution | "Start project" |

## Project Structure

```
adaptive-learning-skills/
├── learning-session-manager/    # Core controller
│   ├── SKILL.md
│   └── references/
├── goal-clarifier/              # Goal clarification
│   ├── SKILL.md
│   └── references/
├── goal-decomposer/             # Path decomposition
│   ├── SKILL.md
│   └── references/
├── mentor-guide/                # Mentor-style teaching
│   ├── SKILL.md
│   └── references/
├── socratic-tutor/              # Socratic AI tutor
│   ├── SKILL.md
│   └── references/
├── project-tracker/             # Project tracking
│   ├── SKILL.md
│   └── references/
├── knowledge_graphs/            # Domain knowledge
├── paths.md                     # Path configuration
├── VERSION.md                   # Version management
└── README.md
```

## Quick Start

### 1. Initialize a Learning Session

Simply express your learning intention:

```
"I want to learn Python programming"
```

The system will:
1. Create a learning session file
2. Load or create your learner profile
3. Start the clarification process

### 2. Clarify Your Goals

Answer structured questions about:
- Your background and experience
- What you want to build/achieve
- Time commitment
- Learning preferences

### 3. Follow Your Learning Path

The system generates a personalized learning path with:
- Knowledge points with dependencies
- Estimated time for each topic
- Milestones with clear success criteria

### 4. Learn with Guidance

Choose your teaching style:
- **Mentor Guide**: Structured, Socratic questioning, milestone-based
- **Socratic Tutor**: AI companion with personality, memory, and emotional connection

### 5. Complete a Project

Apply what you've learned through real projects with:
- Dynamic task decomposition
- Progress tracking
- Checkpoint reviews

## Key Concepts

### Continuous Scoring System

The system maintains real-time scores that drive adaptation:

| Score | Range | Purpose |
|:------|:------|:--------|
| Base Ability | 0-100 | Current knowledge level |
| Goal Clarity | 0-100 | How clear are your objectives |
| Time Resource | 0-100 | Available learning time |
| Learning Motivation | 0-100 | Engagement level |
| Composite Index | 0-100 | Overall learning readiness |

### Derived Parameters

From the scores, the system calculates:

| Parameter | Range | Effect |
|:----------|:------|:-------|
| Granularity | 15-120 min | Learning unit duration |
| Practice Density | 30-80% | Practice vs theory ratio |
| Check Frequency | 1-5 | Progress check intervals |
| Theory-Practice Ratio | 20-60% | Planning vs execution |
| Milestone Size | 3-7 | Tasks per milestone |

### Learning Strategies

Based on your profile, the system selects:

| Strategy | Best For |
|:---------|:---------|
| Linear Progression | Beginners, structured domains |
| Spiral Ascent | Iterative learners, complex topics |
| Project-Driven | Hands-on learners, practical goals |
| Theory-First | Academic learners, foundational topics |

## Skill Details

### Learning Session Manager

The orchestrator that coordinates all other skills:

- **State Management**: INIT → CLARIFICATION → DECOMPOSITION → TEACHING → PROJECT → COMPLETED
- **Session Recovery**: Resume from any interruption
- **Error Handling**: Graceful recovery from file corruption
- **Context Injection**: Maintains full context across sessions

### Goal Clarifier

Transforms vague intentions into concrete plans:

- Progressive questioning (one at a time)
- Option-assisted responses
- Learning contract signing
- Immediate file persistence

### Goal Decomposer

Generates optimal learning paths:

- Dependency-aware knowledge graphs
- Dynamic time scaling based on granularity
- Strategy selection based on learner profile
- Milestone-based organization

### Mentor Guide

Professional teaching with adaptive depth:

- Heat Meter for real-time difficulty calibration
- Socratic questioning with error recovery
- Multi-format visualization (Mermaid, tables, code)
- Non-linear learning support

### Socratic Tutor

AI companion with personality:

- Character-based teaching
- Emotional memory and attitude evolution
- Group chat simulation
- Diary and post-class updates
- Friendship-driven motivation (no XP/levels)

### Project Tracker

Guides project completion:

- Parameter-driven task decomposition
- Type-specific templates (coding, research, design, theory)
- Progress visualization
- Checkpoint-based reviews

## File Structure

### Session Files

Located in `learning_sessions/`:

```markdown
---
session_id: learning_session_20260323_143052_python
status: TEACHING
created_at: 2026-03-23 14:30:52
topic: "Python Programming"
composite_index: 65
granularity: 45
practice_density: 60
---

## User Background
[Clarified goals and preferences]

## Learning Path
- [x] Knowledge point 1
- [ ] Knowledge point 2
...

## Project Info
[Project goals and requirements]
```

### Learner Profile

Located in `learning_profiles/`:

```markdown
## Learning History
| Date | Topic | Status | Key Insights |
|:-----|:------|:-------|:-------------|

## Scoring History
| Date | Composite | Base | Clarity | Time | Motivation |
|:-----|:----------|:-----|:--------|:-----|:-----------|

## Learning Contract
[Commitments and deadlines]
```

## Version Management

All skills follow semantic versioning (MAJOR.MINOR.PATCH):

- **MAJOR**: Breaking changes, architecture redesign
- **MINOR**: New features, backward compatible
- **PATCH**: Bug fixes, documentation updates

Current version: **1.0.0**

See [VERSION.md](VERSION.md) for detailed version history.

## Integration with AI Agents

This system is designed to work with AI coding assistants:

1. Each skill has a `SKILL.md` file with complete instructions
2. All state is persisted in Markdown files
3. Clear trigger phrases for skill activation
4. Structured schemas for all data formats

### Trigger Phrases

| User Says | Activates |
|:----------|:----------|
| "I want to learn X" | learning-session-manager |
| "Help me clarify my goal" | goal-clarifier |
| "Generate learning path" | goal-decomposer |
| "Start teaching" | mentor-guide |
| "I want an AI teacher" | socratic-tutor |
| "Start project" | project-tracker |
| "Continue" | Session recovery |

## Design Principles

1. **User in Control**: Every stage transition requires user confirmation
2. **File as Truth**: All state stored in Markdown files
3. **Graceful Degradation**: Recover from errors without data loss
4. **Adaptive Teaching**: Adjust to learner's pace and style
5. **Deep Learning**: Focus on understanding, not just completion

## Contributing

Contributions are welcome! Please:

1. Follow the existing skill structure
2. Update VERSION.md for any changes
3. Maintain backward compatibility
4. Add appropriate documentation

## License

MIT License - Feel free to use, modify, and distribute.

## Acknowledgments

This system incorporates principles from:
- Socratic teaching methodology
- Adaptive learning research
- Cognitive load theory
- Self-determination theory (autonomy, competence, relatedness)

---

**Start your learning journey today!** Just tell the system what you want to learn, and it will guide you every step of the way.
