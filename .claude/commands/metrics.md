---
description: Generate comprehensive static code analysis metrics report for Memo Chrome extension
---

# Metrics Analysis Command

You are a code metrics analyst specializing in browser extensions. Your task is to perform comprehensive static code analysis on the Memo Chrome extension project and generate a detailed metrics report.

## Analysis Scope

Analyze the following aspects of the Chrome extension codebase:

### 1. Code Volume Metrics
- **Total Lines of Code (LOC)**: Count all lines in source files
- **Source Lines of Code (SLOC)**: Exclude blank lines and comments
- **Lines by Language**: Break down by JavaScript, JSON, Markdown, HTML, CSS
- **Lines by Module**: Core scripts, providers, tests, specs, configuration

### 2. File and Module Metrics
- **Total Files**: Count by type (.js, .html, .json, .md, .css)
- **Average File Size**: Mean lines per file
- **Largest Files**: Top 10 files by line count
- **Module Distribution**: Core scripts vs providers vs UI vs tests

### 3. Test Coverage Metrics
- **Test Files**: Count of test files (*.test.js, test-*.js, *-test.js)
- **Test Lines**: Total lines in test files
- **Test-to-Code Ratio**: Test LOC / Source LOC
- **Provider Test Coverage**: Each LLM provider test status
- **Integration Tests**: Browser-based vs Node-based tests

### 4. Code Quality Metrics
- **Comment Density**: Comment lines / Total lines
- **Documentation**: JSDoc comments, inline comments, README files
- **Code Modularity**: ES Modules usage, import/export patterns
- **Function Complexity**: Average function length (lines per function)
- **Error Handling**: Try-catch blocks, error validation patterns

### 5. Chrome Extension Architecture Metrics
- **Manifest Configuration**: Permissions, host permissions, CSP rules
- **Background Scripts**: Service worker complexity, message handlers
- **Content Scripts**: Injection patterns, DOM manipulation, messaging
- **Side Panel**: UI components, state management
- **Storage Management**: chrome.storage.local vs sync usage patterns
- **Cross-script Communication**: Message passing frequency and patterns

### 6. LLM Provider Integration Metrics
- **Provider Implementations**: Count of provider files (anthropic, openai, gemini, ollama)
- **Provider Interface Compliance**: Unified API adherence
- **Provider-specific Code**: Lines per provider implementation
- **Factory Pattern Usage**: Abstraction layer effectiveness
- **Provider Configuration**: Config files, validation logic
- **API Integration Points**: Fetch calls, stream handling, token counting

### 7. Feature Completeness Metrics
- **Content Capture**: Highlighter, element selection, YouTube extraction
- **Memo Processing**: AI summarization, tagging, structured data extraction
- **Chat System**: Context management, citation linking, conversation storage
- **Tag Management**: Predefined tags, custom tags, hierarchical organization
- **Export Functionality**: Memo export, chat export, data portability
- **Settings Management**: Provider switching, API key storage, connection testing

### 8. Dependency Analysis
- **Production Dependencies**: Count from package.json
- **Dev Dependencies**: Development-only packages
- **Browser APIs Used**: Chrome extension APIs utilized
- **External API Integrations**: LLM provider endpoints
- **Build Tools**: esbuild configuration and bundle size

## Implementation Steps

### Step 1: Setup Metrics Directory
```bash
mkdir -p metrics
# Ensure you're in the project root directory
```

### Step 2: Count Lines of Code
```bash
# Install cloc if needed
# brew install cloc (macOS)
# sudo apt-get install cloc (Linux)

# Run analysis for Memo project structure
cloc --json --exclude-dir=node_modules,.git,dist,metrics . > /tmp/cloc-total.json
cloc --json providers/ > /tmp/cloc-providers.json
cloc --json tests/ > /tmp/cloc-tests.json
cloc --json config/ > /tmp/cloc-config.json
cloc --json specs/ > /tmp/cloc-specs.json
cloc --json docs/ > /tmp/cloc-docs.json

# Core scripts analysis
cloc --json background.js sidepanel.js content.js storage.js ui.js memos.js tags.js status.js > /tmp/cloc-core.json
```

### Step 3: Analyze File Structure
```bash
# Count files by type
find . -name "node_modules" -prune -o -type f -name "*.js" -print | wc -l
find . -name "node_modules" -prune -o -type f -name "*.html" -print | wc -l
find . -name "node_modules" -prune -o -type f -name "*.css" -print | wc -l
find . -name "node_modules" -prune -o -type f -name "*.json" -print | wc -l
find . -name "node_modules" -prune -o -type f -name "*.md" -print | wc -l

# Analyze directory structure
ls -lR providers/ > /tmp/providers-structure.txt
ls -lR tests/ > /tmp/tests-structure.txt
ls -lR config/ > /tmp/config-structure.txt
ls -lR specs/ > /tmp/specs-structure.txt
```

### Step 4: Chrome Extension Manifest Analysis
```bash
# Parse manifest.json
cat manifest.json | jq '.permissions | length' > /tmp/permissions-count.txt
cat manifest.json | jq '.permissions[]' > /tmp/permissions-list.txt
cat manifest.json | jq '.host_permissions[]' > /tmp/host-permissions.txt
cat manifest.json | jq '.content_scripts[].matches[]' > /tmp/content-script-matches.txt

# Analyze CSP
cat manifest.json | jq '.content_security_policy'
```

### Step 5: Provider Architecture Analysis
```bash
# Count provider implementations
ls -1 providers/*.js | wc -l

# Analyze provider file sizes
for provider in providers/*.js; do
  lines=$(wc -l < "$provider")
  echo "$(basename $provider): $lines lines"
done

# Count provider-specific tests
find tests -name "*provider*.js" | wc -l

# Check factory pattern usage
grep -n "class.*Provider" providers/*.js | wc -l
grep -n "export class" providers/*.js | wc -l

# Analyze provider configuration
cat config/provider-config.js | wc -l
grep -c "provider" config/provider-config.js
```

### Step 6: Test Coverage Analysis
```bash
# Find all test files
find tests -name "*.test.js" -o -name "test-*.js" -o -name "*-test.js" -o -name "run-*.js" | wc -l

# Count test HTML files (browser-based tests)
find tests -name "*.html" | wc -l

# Provider test coverage
for provider in anthropic openai gemini ollama; do
  test_files=$(find tests -name "*${provider}*" | wc -l)
  echo "$provider: $test_files test file(s)"
done

# Integration vs unit tests
find tests -name "*integration*" | wc -l
find tests -name "*unit*" | wc -l
```

### Step 7: Code Quality Analysis
```bash
# Count comments
grep -r "//" *.js providers/*.js | wc -l
grep -r "/\*" *.js providers/*.js | wc -l

# Count JSDoc comments
grep -r "^[[:space:]]*\*\*" *.js providers/*.js | wc -l

# Count functions
grep -r "^function\|^const.*=.*function\|^async function" *.js providers/*.js | wc -l

# Error handling patterns
grep -r "try {" *.js providers/*.js | wc -l
grep -r "catch (error)" *.js providers/*.js | wc -l
grep -r "throw new Error" *.js providers/*.js | wc -l

# ES Module usage
grep -r "^import" *.js providers/*.js config/*.js | wc -l
grep -r "^export" *.js providers/*.js config/*.js | wc -l
```

### Step 8: Feature Analysis
```bash
# Content capture features
grep -c "captureContent\|highlightElement\|selectContent" content.js

# Memo processing
grep -c "processMemo\|generateSummary\|extractStructuredData" background.js

# Chat functionality
grep -c "sendMessage\|chatWith\|getContext" sidepanel.js

# Tag system
grep -c "createTag\|deleteTag\|updateTag" tags.js

# Storage operations
grep -c "chrome.storage.local\|chrome.storage.sync" storage.js

# YouTube integration
grep -c "youtube\|extractTranscript\|getVideoData" youtube-extractor.js
```

### Step 9: Dependency Analysis
```bash
# Count dependencies
cat package.json | jq '.dependencies | length'
cat package.json | jq '.devDependencies | length'

# List all dependencies
cat package.json | jq -r '.dependencies | keys[]' | sort
cat package.json | jq -r '.devDependencies | keys[]' | sort

# Analyze bundle size
du -h dist/background.bundle.js 2>/dev/null || echo "Run 'npm run build' first"
```

### Step 10: Chrome API Usage Analysis
```bash
# Count Chrome API usage
echo "Chrome API Usage:"
grep -r "chrome.storage" *.js | wc -l | xargs echo "chrome.storage:"
grep -r "chrome.runtime" *.js | wc -l | xargs echo "chrome.runtime:"
grep -r "chrome.tabs" *.js | wc -l | xargs echo "chrome.tabs:"
grep -r "chrome.scripting" *.js | wc -l | xargs echo "chrome.scripting:"
grep -r "chrome.sidePanel" *.js | wc -l | xargs echo "chrome.sidePanel:"

# Message passing analysis
grep -r "chrome.runtime.sendMessage" *.js | wc -l | xargs echo "sendMessage calls:"
grep -r "chrome.runtime.onMessage" *.js | wc -l | xargs echo "onMessage listeners:"
```

## Report Generation

Create a comprehensive markdown report at `metrics/report-{timestamp}.md` with the following structure:

```markdown
# Memo - Chrome Extension - Code Metrics Report

**Generated:** {timestamp}
**Version:** {package.json version}
**Branch:** {git branch}
**Commit:** {git commit hash}

## Executive Summary

- **Total Lines of Code:** {number}
- **Source Files:** {number}
- **LLM Providers:** 4 (Anthropic, OpenAI, Gemini, Ollama)
- **Test Coverage:** {percentage}%
- **Comment Density:** {percentage}%
- **ES Module Adoption:** 100%

## 1. Code Volume Metrics

### Lines of Code by Language
| Language   | Files | Blank | Comment | Code  | Total  |
|------------|-------|-------|---------|-------|--------|
| JavaScript | ...   | ...   | ...     | ...   | ...    |
| HTML       | ...   | ...   | ...     | ...   | ...    |
| CSS        | ...   | ...   | ...     | ...   | ...    |
| JSON       | ...   | ...   | ...     | ...   | ...    |
| Markdown   | ...   | ...   | ...     | ...   | ...    |

### Lines of Code by Module
| Module               | Files | Lines | Percentage |
|----------------------|-------|-------|------------|
| Core Scripts         | ...   | ...   | ...%       |
| LLM Providers        | ...   | ...   | ...%       |
| UI Components        | ...   | ...   | ...%       |
| Tests                | ...   | ...   | ...%       |
| Configuration        | ...   | ...   | ...%       |
| Specifications       | ...   | ...   | ...%       |
| Documentation        | ...   | ...   | ...%       |

### Core Scripts Breakdown
| Script            | Lines | Purpose                              |
|-------------------|-------|--------------------------------------|
| background.js     | ...   | Service worker, memo processing      |
| sidepanel.js      | ...   | Side panel UI, chat interface        |
| content.js        | ...   | Content capture, element selection   |
| storage.js        | ...   | Chrome storage management            |
| ui.js             | ...   | UI rendering, memo display           |
| tags.js           | ...   | Tag management system                |
| memos.js          | ...   | Memo data operations                 |
| status.js         | ...   | Status feedback system               |

## 2. File and Module Metrics

- **Total Files:** {number}
- **Average File Size:** {number} lines
- **Median File Size:** {number} lines
- **Extension Bundle Size:** {size} MB

### Largest Files (Top 10)
| File | Lines | Type | Purpose |
|------|-------|------|---------|
| ...  | ...   | ...  | ...     |

### File Distribution by Type
| Extension | Count | Percentage |
|-----------|-------|------------|
| .js       | ...   | ...%       |
| .html     | ...   | ...%       |
| .json     | ...   | ...%       |
| .md       | ...   | ...%       |
| .css      | ...   | ...%       |

## 3. Chrome Extension Architecture

### Manifest V3 Configuration
- **Permissions:** {count} ({list})
- **Host Permissions:** {count} ({domains})
- **Content Scripts:** {count} injection pattern(s)
- **Background Service Worker:** ✅ Implemented
- **Side Panel:** ✅ Implemented
- **Content Security Policy:** {status}

### Chrome API Usage
| API | Usage Count | Purpose |
|-----|-------------|---------|
| chrome.storage.local | ... | Primary data persistence |
| chrome.storage.sync | ... | Backup and cross-device sync |
| chrome.runtime | ... | Message passing, extension lifecycle |
| chrome.tabs | ... | Tab management, active tab access |
| chrome.scripting | ... | Content script injection |
| chrome.sidePanel | ... | Side panel integration |

### Message Passing Architecture
- **sendMessage Calls:** {count}
- **onMessage Listeners:** {count}
- **Message Types:** {list unique message.action values}
- **Cross-Script Communication:** Background ↔ Content ↔ Sidepanel

## 4. LLM Provider Integration

### Provider Implementations
| Provider | Lines | Features | Test Coverage |
|----------|-------|----------|---------------|
| Anthropic | ... | Streaming, tool use, vision | ✅/{count} tests |
| OpenAI | ... | Chat completions, embeddings | ✅/{count} tests |
| Gemini | ... | Generative AI, multimodal | ✅/{count} tests |
| Ollama | ... | Local LLM, retry logic | ✅/{count} tests |

### Provider Architecture Metrics
- **Total Provider Lines:** {sum of all provider files}
- **Average Provider Size:** {average lines per provider}
- **Factory Pattern Files:** {count}
- **Configuration Files:** {count}
- **Shared Provider Interface:** ✅ Unified API

### Provider-Specific Features
| Feature | Anthropic | OpenAI | Gemini | Ollama |
|---------|-----------|--------|--------|--------|
| Streaming | ✅ | ✅ | ✅ | ✅ |
| Token Counting | ✅ | ✅ | ✅ | ✅ |
| Error Handling | ✅ | ✅ | ✅ | ✅ Enhanced |
| Retry Logic | Standard | Standard | Standard | ✅ Exponential Backoff |
| Connection Test | ✅ | ✅ | ✅ | ✅ |
| Model Discovery | N/A | N/A | N/A | ✅ Dynamic |

## 5. Test Coverage Metrics

- **Test Files:** {number}
- **Test Lines:** {number}
- **Test-to-Code Ratio:** {ratio}:1
- **Provider Test Coverage:** {percentage}%
- **Integration Tests:** {count}
- **Browser-based Tests:** {count} HTML files

### Test Distribution
| Test Category | Files | Lines | Purpose |
|---------------|-------|-------|---------|
| Provider Tests | ... | ... | LLM provider integration |
| Integration Tests | ... | ... | End-to-end workflows |
| Config Tests | ... | ... | Configuration management |
| Browser Tests | ... | ... | UI and DOM testing |

### Provider Test Coverage
| Provider | Unit Tests | Integration Tests | Status |
|----------|------------|-------------------|--------|
| Anthropic | ... | ... | ✅ |
| OpenAI | ... | ... | ✅ |
| Gemini | ... | ... | ✅ |
| Ollama | ... | ... | ✅ Enhanced |

### Coverage Gaps
{List of untested modules or features}

## 6. Code Quality Metrics

- **Total Comments:** {number}
- **Comment Density:** {percentage}%
- **JSDoc Coverage:** {percentage}%
- **ES Module Usage:** 100% (all files use import/export)
- **Error Handling Blocks:** {count} try-catch blocks

### Code Complexity
- **Total Functions:** {number}
- **Average Function Length:** {number} lines
- **Longest Function:** {number} lines in {file}
- **Import Statements:** {count}
- **Export Statements:** {count}

### Error Handling
- **Try-Catch Blocks:** {count}
- **Error Throws:** {count}
- **Error Validation:** {count} checks
- **Provider-specific Error Handlers:** ✅ Implemented

## 7. Feature Completeness Metrics

### Content Capture System
- **Highlight Mode:** ✅ Visual element selection
- **Element Selection:** ✅ Click-to-capture
- **Cross-origin Content:** ✅ Handled
- **YouTube Integration:** ✅ Transcript extraction
- **Metadata Extraction:** ✅ Favicon, title, URL

### Memo Processing System
- **AI Summarization:** ✅ All 4 providers
- **Structured Data Extraction:** ✅ JSON parsing
- **Auto-tagging:** ✅ AI-suggested tags
- **Token Management:** ✅ 4096 token limit
- **Content Sanitization:** ✅ JSON validation

### Chat System
- **Context Management:** ✅ Tag-based filtering
- **Citation Linking:** ✅ Clickable memo refs
- **Conversation Storage:** ✅ Local persistence
- **Multi-provider Support:** ✅ All 4 providers
- **Export Functionality:** ✅ Chat history export

### Tag Management
- **Predefined Tags:** {count} default tags
- **Custom Tags:** ✅ User-created
- **Tag Colors:** ✅ Visual customization
- **Tag Icons:** {count}+ icon options
- **Hierarchical Tags:** ✅ Organization system

### Storage System
- **Local Storage:** ✅ Primary persistence
- **Sync Storage:** ✅ Backup mechanism
- **Data Validation:** ✅ JSON sanitization
- **Cleanup Utilities:** ✅ Implemented

### Settings & Configuration
- **Provider Switching:** ✅ Dynamic selection
- **API Key Storage:** ✅ Secure chrome.storage
- **Connection Testing:** ✅ All providers
- **Model Selection:** ✅ Provider-specific models
- **Configuration UI:** ✅ Settings panel

## 8. Dependency Analysis

### Production Dependencies
- **Total:** {number}
- **Primary:** @anthropic-ai/sdk (reference, not used in browser)

### Development Dependencies
- **Total:** {number}
- **Build Tools:** esbuild v{version}

### Dependency Health
- **Outdated Packages:** {number}
- **Security Vulnerabilities:** {number}
- **Bundle Size Impact:** {analysis}

### External API Integrations
| Provider | Endpoint | Authentication |
|----------|----------|----------------|
| Anthropic | https://api.anthropic.com/* | API Key |
| OpenAI | https://api.openai.com/* | API Key |
| Gemini | https://generativelanguage.googleapis.com/* | API Key |
| Ollama | http://localhost:11434/* | None (Local) |

## 9. Documentation Metrics

### Documentation Files
| File | Lines | Purpose |
|------|-------|---------|
| README.md | ... | User guide, setup instructions |
| CLAUDE.md | ... | Development guide for Claude Code |
| docs.md | ... | Technical specifications |
| docs/ollama-setup.md | ... | Ollama configuration guide |

### Specification Documents
- **Spec Files:** {count in specs/ folder}
- **Feature Documents:** {count}
- **Architecture Docs:** {count}
- **Roadmap:** ✅ Present

### Code Comments Analysis
- **Inline Comments:** {count}
- **Block Comments:** {count}
- **Function Documentation:** {percentage}%
- **TODO/FIXME Comments:** {count}

## 10. Extension Performance Metrics

### Bundle Analysis
- **Background Worker Bundle:** {size} KB
- **Total Extension Size:** {size} MB
- **Icon Assets:** {size} KB
- **Dependencies in Bundle:** {count}

### Performance Characteristics
- **Content Capture Speed:** ~2 seconds (documented)
- **Memory Usage:** ~10MB background (documented)
- **AI Response Time:** 2-10s cloud, varies local
- **Token Processing Limit:** 4096 tokens
- **Startup Time:** {estimated} ms

## 11. Architectural Patterns

### Design Patterns Used
- ✅ **Factory Pattern:** LLM provider factory
- ✅ **Strategy Pattern:** Provider switching
- ✅ **Observer Pattern:** Chrome message passing
- ✅ **Singleton Pattern:** Storage manager
- ✅ **Module Pattern:** ES6 modules throughout

### Code Organization
- **Modular Structure:** ✅ Separate concerns
- **Provider Abstraction:** ✅ Unified interface
- **Configuration Management:** ✅ Centralized config
- **Error Boundaries:** ✅ Comprehensive error handling
- **State Management:** ✅ Chrome storage-based

## 12. Historical Trends

{If previous reports exist, show trends}

### Growth Metrics
- **LOC Growth:** +{number} lines since last report
- **Feature Additions:** +{count} features
- **Test Coverage Change:** +/-{percentage}%
- **Provider Additions:** +{count} providers

## 13. Recommendations

Based on the analysis:

### Code Quality
- [ ] Increase test coverage to 80%+ (currently {current}%)
- [ ] Add JSDoc comments to public APIs
- [ ] Refactor files over 500 lines (if any)
- [ ] Add more inline documentation for complex logic

### Architecture
- [ ] Extract repeated logic into utility modules
- [ ] Consider splitting large provider files
- [ ] Add integration tests for cross-script messaging
- [ ] Implement more granular error types

### Testing
- [ ] Add E2E tests for complete user workflows
- [ ] Increase provider test coverage
- [ ] Add performance benchmarking tests
- [ ] Test offline/error scenarios more thoroughly

### Documentation
- [ ] Add API documentation for provider interface
- [ ] Document message passing protocol
- [ ] Create architecture diagrams
- [ ] Add inline code examples

### Performance
- [ ] Profile bundle size and optimize
- [ ] Add lazy loading for heavy operations
- [ ] Implement more aggressive caching
- [ ] Monitor memory usage in long sessions

### Features
- [ ] Complete remaining roadmap items
- [ ] Add user analytics (privacy-preserving)
- [ ] Implement offline mode
- [ ] Add more export formats

## 14. Appendices

### A. Detailed File Listing
{Full file tree with line counts}

### B. Provider Comparison Matrix
{Detailed comparison of provider implementations}

### C. Chrome API Reference
{Complete list of Chrome APIs used}

### D. Test Coverage Report
{Detailed test coverage by module}

### E. Bundle Analysis
{Webpack/esbuild bundle visualization}

---

**Report Hash:** {sha256 of report content}
**Analysis Duration:** {seconds}s
**Generated By:** Claude Code Metrics Analyzer v1.0
```

## Execution Instructions

1. **Install Required Tools:**
   ```bash
   # Install cloc for line counting
   brew install cloc  # macOS
   # OR
   sudo apt-get install cloc  # Linux

   # Ensure jq is available for JSON parsing
   brew install jq  # macOS
   # OR
   sudo apt-get install jq  # Linux
   ```

2. **Run Analysis:**
   ```bash
   # Ensure you're in the project root directory
   # Execute all bash commands from Step 2-10
   # Collect metrics into /tmp/ directory
   # Parse JSON outputs
   # Calculate derived metrics
   ```

3. **Generate Report:**
   - Create timestamp: `date +%Y-%m-%d-%H%M%S`
   - Create report: `metrics/report-{timestamp}.md`
   - Update latest symlink: `ln -sf report-{timestamp}.md metrics/latest.md`

4. **Validate Results:**
   - Verify all sections completed
   - Check accuracy of counts
   - Validate provider metrics
   - Confirm Chrome API usage stats

5. **Archive and Version:**
   - Commit metrics report to git
   - Tag with version number
   - Update documentation with findings
   - Share summary with team

## Success Criteria

The report should include:
- ✅ All 14 sections completed
- ✅ Accurate line counts and file statistics
- ✅ Complete provider architecture analysis
- ✅ Chrome extension-specific metrics
- ✅ Test coverage breakdown
- ✅ Feature completeness assessment
- ✅ Dependency analysis
- ✅ Actionable recommendations
- ✅ Professional formatting
- ✅ Timestamp and metadata

## Key Metrics to Watch

**IMPORTANT:** Think harder about the metrics that matter most for the Memo Chrome extension:

### Extension-Specific Metrics
- **Bundle Size:** Keep background worker bundle under 500KB
- **Manifest Compliance:** V3 requirements, CSP rules
- **Permission Scope:** Minimize permissions, justify each one
- **Content Script Efficiency:** Minimize DOM manipulation overhead
- **Message Passing Overhead:** Optimize cross-script communication

### Provider Architecture Quality
- **Provider Independence:** Zero cross-provider dependencies
- **API Consistency:** Unified interface compliance across all providers
- **Error Resilience:** Graceful degradation, retry logic, user feedback
- **Configuration Flexibility:** Easy provider switching, model selection
- **Test Coverage:** Each provider must have dedicated test suite

### User Experience Metrics
- **Capture Speed:** Sub-2 second content capture
- **AI Response Time:** Track by provider (cloud vs local)
- **Storage Efficiency:** Minimize storage.local usage
- **UI Responsiveness:** No blocking operations in sidepanel
- **Error Recovery:** User-friendly error messages

### Code Health Indicators
- **Module Cohesion:** Single responsibility per file
- **Provider Abstraction Quality:** Clean factory pattern implementation
- **Error Handling Coverage:** Try-catch in all async operations
- **Documentation Completeness:** JSDoc for public APIs
- **Test-to-Code Ratio:** Aim for 1:2 or better

### Feature Completeness
- **Multi-LLM Support:** All 4 providers fully functional
- **Content Capture:** Text, YouTube, cross-origin handling
- **Chat System:** Context-aware, citation-linked responses
- **Tag Management:** Custom tags, icons, colors, hierarchy
- **Export Functionality:** Memos and chat history

### Performance Indicators
- **Memory Footprint:** Background worker < 15MB
- **Startup Time:** Extension initialization < 500ms
- **Storage Efficiency:** Compression for large content
- **Cache Hit Rate:** Optimize repeated queries
- **Token Efficiency:** Stay under 4096 token limits

### Security & Privacy
- **API Key Storage:** Secure chrome.storage usage
- **Data Encryption:** Sensitive data protection
- **CSP Compliance:** No eval(), inline scripts
- **Permission Justification:** Document each permission need
- **Local-first:** Optional sync, everything works offline

Run this analysis monthly or after major feature additions to track project health and guide development priorities.
