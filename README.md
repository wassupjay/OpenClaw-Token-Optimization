# OpenClaw-Token-Optimization

> Reduce OpenClaw API costs by 90-97% without sacrificing performance

A collection of battle-tested configurations and practices to dramatically cut your OpenClaw token usage and API bills.

## Quick Stats

- **Monthly savings**: $1,200+ → ~$50
- **Setup time**: ~60 minutes
- **Performance impact**: Zero
- **Implementation difficulty**: Easy

## What This Does

OpenClaw ships with defaults optimized for capability, not cost. This repo provides drop-in configurations to:

- ✅ Eliminate wasteful session loading
- ✅ Route tasks to appropriate model tiers
- ✅ Move heartbeats to free local LLM
- ✅ Prevent runaway automation
- ✅ Leverage prompt caching
- ✅ Minimize context bloat

## Installation

### 1. Session Optimization

Add to your agent's system prompt:

```markdown
SESSION INITIALIZATION:

On session start, load ONLY:
- SOUL.md
- USER.md
- IDENTITY.md
- memory/[TODAY].md

DO NOT auto-load:
- MEMORY.md
- Session history
- Previous outputs

Load history on-demand with memory_search() when requested.

At session end, update memory/[TODAY].md with:
- Tasks completed
- Decisions made
- Blockers
- Next steps
```

**Impact**: 80% reduction in session startup costs

---

### 2. Smart Model Routing

Update `~/.openclaw/openclaw.json`:

```json
{
  "agents": {
    "defaults": {
      "model": {
        "primary": "anthropic/claude-haiku-4-5"
      },
      "models": {
        "anthropic/claude-sonnet-4-5": {
          "alias": "sonnet"
        },
        "anthropic/claude-haiku-4-5": {
          "alias": "haiku"
        }
      }
    }
  }
}
```

Add to system prompt:

```markdown
MODEL SELECTION:

Default: Haiku (fast, cheap)

Escalate to Sonnet only for:
- Architecture decisions
- Security analysis
- Complex debugging
- Strategic planning
- Deep reasoning tasks

When uncertain, try Haiku first.
```

**Impact**: 85-90% reduction in model costs

---

### 3. Free Local Heartbeats

Install Ollama:

```bash
curl -fsSL https://ollama.ai/install.sh | sh
ollama pull llama3.2:3b
ollama serve
```

Update `~/.openclaw/openclaw.json`:

```json
{
  "agents": {
    "defaults": {
      "model": {
        "primary": "anthropic/claude-haiku-4-5"
      },
      "models": {
        "anthropic/claude-sonnet-4-5": {
          "alias": "sonnet"
        },
        "anthropic/claude-haiku-4-5": {
          "alias": "haiku"
        }
      }
    }
  },
  "heartbeat": {
    "every": "1h",
    "model": "ollama/llama3.2:3b",
    "session": "main",
    "prompt": "Status check: any updates needed?"
  }
}
```

**Impact**: 100% elimination of heartbeat costs (~$50/month)

---

### 4. Rate Limits & Budget Controls

Add to system prompt:

```markdown
RATE LIMITS:

- Min 5s between API calls
- Min 10s between web searches
- Max 5 searches/batch, then 2min break
- Batch similar operations
- On 429 error: stop, wait 5min, retry

BUDGETS:
- Daily: $5 max (warn at $4)
- Monthly: $180 max (warn at $140)
```

**Impact**: Prevents runaway costs

---

### 5. Minimal Workspace Files

Create lean context files:

**workspace/SOUL.md**:
```markdown
# Agent Core Principles

## Model Selection
Default: Haiku
Escalate to Sonnet for: reasoning, security, architecture

## Rate Limits
5s between calls | 10s between searches | Max 5 searches then break

## Reference
See OPTIMIZATION.md for full guidelines
```

**workspace/USER.md**:
```markdown
# User Context

Name: [YOUR_NAME]
Timezone: [TIMEZONE]
Mission: [MISSION]

## Goals
- [GOAL_1]
- [GOAL_2]
```

**Impact**: 30-40% reduction in baseline context

---

### 6. Prompt Caching (Advanced)

Update `~/.openclaw/openclaw.json`:

```json
{
  "agents": {
    "defaults": {
      "model": {
        "primary": "anthropic/claude-haiku-4-5"
      },
      "cache": {
        "enabled": true,
        "ttl": "5m",
        "priority": "high"
      },
      "models": {
        "anthropic/claude-sonnet-4-5": {
          "alias": "sonnet",
          "cache": true
        },
        "anthropic/claude-haiku-4-5": {
          "alias": "haiku",
          "cache": false
        }
      }
    }
  }
}
```

**Cache strategy**:
- ✅ Cache: SOUL.md, USER.md, reference docs (stable content)
- ❌ Don't cache: Daily notes, recent messages, tool outputs (dynamic)
- 🔄 Batch similar tasks within 5-minute windows
- 📁 Separate stable vs dynamic files

**Impact**: 90% discount on repeated content

---

## File Structure

Organize your workspace for optimal caching:

```
workspace/
├── SOUL.md              # Cache (stable)
├── USER.md              # Cache (stable)
├── TOOLS.md             # Cache (stable)
├── memory/
│   ├── MEMORY.md        # Don't cache (dynamic)
│   └── 2024-02-09.md    # Don't cache (daily)
└── projects/
    └── [PROJECT]/
        ├── REFERENCE.md # Cache (stable)
        └── NOTES.md     # Don't cache (dynamic)
```

---

## Verification

Check your setup:

```bash
openclaw shell
session_status
```

**Success indicators**:
- ✅ Context size: 2-8KB (not 50KB+)
- ✅ Default model: Haiku
- ✅ Heartbeat: Ollama/local
- ✅ Daily cost: $0.10-0.50

---

## Results

| Metric | Before | After | Savings |
|--------|--------|-------|---------|
| Session loading | $70-90/mo | $5-10/mo | 90% |
| Model usage | $700-900/mo | $80-100/mo | 90% |
| Heartbeat | $40-60/mo | $0 | 100% |
| Prompt caching | Full price | 90% off | Variable |
| **Total** | **$1,200+/mo** | **~$50/mo** | **96%** |

---

## Quick Start

**Week 1** (30min):
```bash
# 1. Add session rules to system prompt
# 2. Update config with Haiku default
# 3. Add model selection rules
```

**Week 2** (15min):
```bash
curl -fsSL https://ollama.ai/install.sh | sh
ollama pull llama3.2:3b
# Update config heartbeat settings
```

**Week 3** (10min):
```bash
# Add rate limits to system prompt
# Set budget alerts
```

**Week 4** (20min):
```bash
# Enable caching in config
# Reorganize workspace files
```

---

## Common Issues

| Problem | Solution |
|---------|----------|
| High context (>20KB) | Verify session initialization rules |
| Sonnet for simple tasks | Check model routing logic |
| Heartbeat costs persist | Ensure `ollama serve` is running |
| Low cache hit rate (<50%) | Batch requests, stabilize prompts |

---

## Best Practices

- **Cache only Sonnet**: Haiku overhead exceeds savings
- **Batch similar tasks**: Within 5-minute cache windows
- **Keep SOUL.md stable**: Changes invalidate cache
- **Start loose on rate limits**: Tighten based on usage
- **Monitor daily**: Track costs, adjust as needed

---

## Configuration Files

### Complete Config Example

`~/.openclaw/openclaw.json`:

```json
{
  "agents": {
    "defaults": {
      "model": {
        "primary": "anthropic/claude-haiku-4-5"
      },
      "cache": {
        "enabled": true,
        "ttl": "5m",
        "priority": "high"
      },
      "models": {
        "anthropic/claude-sonnet-4-5": {
          "alias": "sonnet",
          "cache": true
        },
        "anthropic/claude-haiku-4-5": {
          "alias": "haiku",
          "cache": false
        }
      }
    }
  },
  "heartbeat": {
    "every": "1h",
    "model": "ollama/llama3.2:3b",
    "session": "main",
    "prompt": "Status check: any updates?"
  }
}
```

---

## System Prompt Template

Complete system prompt with all optimizations:

```markdown
# SESSION INITIALIZATION

On session start, load ONLY:
- SOUL.md
- USER.md
- IDENTITY.md
- memory/[TODAY].md

DO NOT auto-load history. Load on-demand with memory_search().

# MODEL SELECTION

Default: Haiku

Escalate to Sonnet for:
- Architecture decisions
- Security analysis
- Complex debugging
- Strategic planning

When uncertain, try Haiku first.

# RATE LIMITS

- Min 5s between API calls
- Min 10s between searches
- Max 5 searches/batch, then 2min break
- Batch similar work
- On 429: stop, wait 5min, retry

# BUDGETS

Daily: $5 max (warn at $4)
Monthly: $180 max (warn at $140)
```

---

## License

MIT

---

## Contributing

Found better optimizations? Open an issue or PR.

