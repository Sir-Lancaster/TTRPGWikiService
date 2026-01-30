# TTRPG Wiki Service - Architecture Notes

> A collaborative wiki tool for multi-GM tabletop RPG campaigns with shared worldbuilding.

---

## Project Overview

### The Problem
A multi-GM D&D game where different players run different worlds with different rulesets. When transitioning between worlds, GM responsibilities and rule systems change. Need a shared repository for notes, maps, and character bios that all GMs can access.

### The Solution
A **shared wiki/Notion-like tool** with:
- Multi-user editing
- World categorization
- Content tagging by rule system
- Map/image attachments
- Character/location databases

---

## Tech Stack

| Layer | Technology | Purpose |
|-------|------------|---------|
| Backend | Django + Django REST Framework | Auth, ORM, permissions, API |
| Frontend | Vite + React | Fast dev server, modern tooling |
| Auth | djangorestframework-simplejwt | JWT token authentication |
| Editor | Tiptap or Quill | Rich text editing for wiki pages |
| Styling | Tailwind CSS or Material-UI | UI components |

---

## Data Model

```
Universe (your group's shared game)
├── Worlds (different rulesets/GMs)
│   ├── Pages (wiki entries)
│   ├── Characters
│   ├── Locations
│   └── Maps/Assets
└── Members (GMs with permissions)
```

### Core Models

```python
from django.db import models
from django.contrib.auth.models import User

class Universe(models.Model):
    """A game group's shared space"""
    name = models.CharField(max_length=200)
    created_at = models.DateTimeField(auto_now_add=True)
    created_by = models.ForeignKey(User, on_delete=models.CASCADE)

class UniverseMembership(models.Model):
    """Who can access which universe"""
    universe = models.ForeignKey(Universe, on_delete=models.CASCADE)
    user = models.ForeignKey(User, on_delete=models.CASCADE)
    role = models.CharField(max_length=20, choices=[
        ('owner', 'Owner'),
        ('gm', 'Game Master'),
        ('viewer', 'Viewer')
    ])
    
    class Meta:
        unique_together = ('universe', 'user')

class World(models.Model):
    """Different campaign settings/rulesets"""
    universe = models.ForeignKey(Universe, on_delete=models.CASCADE)
    name = models.CharField(max_length=200)
    ruleset = models.CharField(max_length=100)  # "D&D 5e", "Pathfinder", etc.
    primary_gm = models.ForeignKey(User, on_delete=models.SET_NULL, null=True)

class Page(models.Model):
    """Wiki-style content pages"""
    world = models.ForeignKey(World, on_delete=models.CASCADE)
    title = models.CharField(max_length=300)
    content = models.TextField()  # HTML or Markdown
    category = models.CharField(max_length=50)  # "NPC", "Location", "Lore", etc.
    created_by = models.ForeignKey(User, on_delete=models.SET_NULL, null=True)
    updated_at = models.DateTimeField(auto_now=True)
    is_public = models.BooleanField(default=False)
```

---

## Permission System

```python
from rest_framework import permissions

class IsUniverseMember(permissions.BasePermission):
    """Only members of the universe can access its content"""
    
    def has_permission(self, request, view):
        universe_id = view.kwargs.get('universe_id')
        return request.user.universemembership_set.filter(
            universe_id=universe_id
        ).exists()

    def has_object_permission(self, request, view, obj):
        universe = self._get_universe(obj)
        return request.user.universemembership_set.filter(
            universe=universe
        ).exists()
    
    def _get_universe(self, obj):
        if hasattr(obj, 'universe'):
            return obj.universe
        elif hasattr(obj, 'world'):
            return obj.world.universe
        return None
```

---

## API Routes

| Method | Endpoint | Description |
|--------|----------|-------------|
| `POST` | `/api/universes/` | Create new game group |
| `GET` | `/api/universes/` | List user's universes |
| `POST` | `/api/universes/{id}/invite/` | Invite user to universe |
| `GET` | `/api/universes/{id}/worlds/` | List worlds in universe |
| `POST` | `/api/universes/{id}/worlds/` | Create new world |
| `GET` | `/api/worlds/{id}/pages/` | List pages in world |
| `POST` | `/api/worlds/{id}/pages/` | Create new page |
| `PATCH` | `/api/pages/{id}/` | Edit page |
| `DELETE` | `/api/pages/{id}/` | Delete page |

---

## Project Structure

```
TTRPGWikiService/
├── backend/                     # Django project
│   ├── manage.py
│   ├── config/                  # Settings, URLs, WSGI
│   │   ├── settings.py
│   │   └── urls.py
│   ├── users/                   # Custom user app
│   ├── universes/               # Core wiki models
│   └── requirements.txt
│
├── frontend/                    # Vite + React
│   ├── src/
│   │   ├── components/
│   │   │   ├── UniverseSelector.jsx
│   │   │   ├── WorldNav.jsx
│   │   │   ├── PageEditor.jsx
│   │   │   ├── PageView.jsx
│   │   │   └── InviteModal.jsx
│   │   ├── pages/
│   │   │   ├── Dashboard.jsx
│   │   │   ├── Universe.jsx
│   │   │   └── Page.jsx
│   │   ├── contexts/
│   │   │   └── UniverseContext.jsx
│   │   └── api/
│   ├── package.json
│   └── vite.config.js
│
└── Architecture-Notes.md
```

---

## Vite Configuration

```javascript
// vite.config.js
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

export default defineConfig({
  plugins: [react()],
  server: {
    port: 5173,
    proxy: {
      '/api': {
        target: 'http://127.0.0.1:8000',
        changeOrigin: true,
      },
    },
  },
})
```

---

## Next Steps

- [ ] Initialize Django backend: `django-admin startproject config backend`
- [ ] Initialize Vite frontend: `npm create vite@latest frontend -- --template react`
- [ ] Set up models (Universe, World, Page, Membership)
- [ ] Configure JWT auth
- [ ] Build permission middleware
- [ ] Create API endpoints
- [ ] Build frontend components

---

## Key Design Decisions

1. **Why Django REST Framework?**
   - Built-in auth system with JWT support
   - ORM handles complex relationships well
   - Admin panel for quick data management
   - Prior experience = faster development

2. **Why separate backend/frontend?**
   - Cleaner separation of concerns
   - Vite provides fast HMR during development
   - Easier to deploy independently

3. **Multi-tenancy approach**
   - Universe-based isolation
   - Permission checks at every API level
   - Scoped queries filter by membership automatically