# Universal Documentation Standards

## DOCUMENTATION STANDARDS

### Project Documentation Structure

#### Organization Philosophy
- **Break up large pages** (>200-300 lines) into focused, topic-specific files
- **Create overview/index pages** with comprehensive navigation rather than monolithic documents
- **Maintain hierarchical organization** with clear parent-child relationships using `docs/topic/subtopic.md` patterns
- **Use consistent directory structures** that group related content logically

#### Navigation Requirements (MANDATORY)
- **Always include breadcrumb navigation** (`← Back to [Parent Page]`) at the top of sub-pages
- **Add comprehensive table of contents** to overview pages with deep links to sections
- **Include cross-reference links** between related sections at page endings
- **Create quick navigation sections** organized by use case, component type, or task
- **Provide multiple navigation paths** (by topic, by component, by workflow)

#### Content Structure Standards
- **Start with clear purpose statements** explaining what each document covers
- **Include practical examples** and command-line instructions where applicable
- **Add "Related Documentation" sections** at the end of pages with links to relevant content
- **Use tables for overview information** with descriptions, counts, and metrics
- **Maintain consistent formatting** and structure patterns across all documentation

#### Specific Patterns to Follow
- **Testing documentation**: Break into focused areas (core logic, security, validation, infrastructure, metrics, integration) with comprehensive overview page
- **Long technical documents** (>200 lines): Add comprehensive TOCs with nested navigation structure
- **Cross-linking strategy**: Each page should link to related pages and provide easy "up" navigation
- **File naming**: Use descriptive, hyphenated names (`core-business-logic.md`, `security-tests.md`)

#### Quality Standards
- **Preserve technical depth** while improving organization and discoverability
- **Keep all critical information** when restructuring - never lose content for the sake of organization
- **Maintain consistency** with existing excellent patterns (like main README navigation)
- **Focus on discoverability** without sacrificing content quality or completeness