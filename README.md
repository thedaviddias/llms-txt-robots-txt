# LLMS Directives for Robots.txt: a Technical Proposal

## 1. Introduction

In September 2024, Jeremy Howard [proposed adding a `/llms.txt`](https://github.com/AnswerDotAI/llms-txt) markdown file to the root of websites to provide LLM-friendly content.

Since then, [more and more websites](https://llmstxthub.com/) and services have been adding `/llms.txt` and `/llms-full.txt` files to provide LLM-friendly content that can be easily consumed by LLMs.

The current proposal aim to define a simple model for communication between users and Large Language Models (LLMs) / Bots / Search Engines through an extension to the existing robots.txt protocol.

The goal is to provide website owners with greater control over how LLMs / Bots / Search Engines interact with their content while offering LLM providers a more efficient way to access appropriately formatted information.

## 2. Problem Statement

Current web crawling mechanisms lack nuanced control for LLM interactions:

- Website owners face a binary choice of allowing or blocking LLMs entirely
- No standardized method exists for directing LLMs to optimized content
- Increased server loads and costs due to indiscriminate crawling
- Risk of content misrepresentation and outdated information in LLM responses
- Challenges in maintaining business models and regulatory compliance

## 3. Proposed Solution: LLMS Directives

### 3.1 Core Concept

A new “LLMS” directive will be added to robots.txt in the form of a new special User Agent for LLMs requesting users, and refers to the directive containing scope of information meant for LLM instruction in a special format. The existing User-agent rules will apply instead of these features.

#### 3.2 Basic Syntax

Skeletal version of the command is given below:

```
LLMS: https://www.example.com/llms.txt
```

The `type` and `locale` would be optional parameters. By default, the type would be `summary` and the locale would be the default language of the website (or english).

For greater precision, the parameter enhanced directives:

```
LLMS: https://www.example.com/en/products/llms.txt; type=summary; category=products; locale=en

LLMS: https://www.example.com/en/products/llms-full.txt; type=full; category=products; locale=en

LLMS: https://www.example.com/es/products/llms.txt; type=summary; category=products; locale=es
```

### 3.3 Parameters

| Parameter | Description | Default | Example Values |
|-----------|-------------|---------|-----------------|
| type | Amount of detail given | summary (for llms.txt), full (for llms-full.txt) | summary, full |
| category | Type of content | none | products, articles, docs, legal |
| locale | Identifies language and region | en | en, es-mx, fr, de |
| version | API/content version | latest | v1, v2, 2023-04 |

## 4. Implementation Examples

### 4.1 E-Commerce Platform


**Scenario**: E-Commerce website that has products in different categories and multiple languages

```
# Standard robots.txt directives
User-agent: *
Disallow: /admin/
Disallow: /checkout/
Allow: /products/

# LLMS directives
LLMS: https://www.example.com/llms.txt;

LLMS: https://www.example.com/llms-full.txt; type=full;

LLMS: https://www.example.com/products/llms.txt; category=products; type=summary;

LLMS: https://www.example.com/blog/llms.txt; category=articles; type=summary;

LLMS: https://www.example.com/es/llms.txt; locale=es;
```

### 4.2 News Organization

**Scenario**: Newspaper with premium content that is usually provided to subscribers and that have legal compliance requirements.

```
# Standard robots.txt directives
User-agent: *
Disallow: /subscribers/
Allow: /headlines/
Allow: /public/

# Directives for LLMS Services

LLMS: <https://www.example.com/headlines/llms.txt>; type=summary; category=news

LLMS: <https://www.example.com/headlines/llms-full.txt>; type=full; category=news

LLMS: <https://www.example.com/legal/llms.txt>; category=legal; type=full

LLMS: <https://www.example.com/attribution/llms.txt>; category=copyright; type=full
```

### 4.3 SaaS Documentation

**Scenario**: Technical documentation with versioned API references

```
# Standard directives for robots.txt file
User-agent: *

Allow: /docs/

Disallow: /internal/

LLMS: <https://www.example.com/docs/llms.txt>; type=summary; category=docs

LLMS: <https://www.example.com/docs/llms-full.txt>; type=full; category=docs

LLMS: <https://www.example.com/api/v1/llms.txt>; category=api; version=v1; type=full

LLMS: <https://www.example.com/api/v2/llms.txt>; category=api; version=v2; type=full

LLMS: <https://www.example.com/docs/tutorial/llms.txt>; category=tutorial; type=full
```

### 4.4 Highly Regulated Industries

**Scenario**: Financial institution with strict compliance requirements

```
# Standard directives for robots.txt
User-agent: *

Allow: /public/

Disallow: /secure/

All and any directives to robots.txt format is simple. This is why any website or service that uses ai has to create more security features implemented for guiding and telling the ai which approach to use where.

LLMS: <https://www.example.com/approved/llms.txt>; type=summary

LLMS: <https://www.example.com/public/products/llms.txt>; category=products; type=summary

LLMS: <https://www.example.com/disclaimers/llms.txt>; category=legal; type=full

LLMS: <https://www.example.com/terms/llms.txt>; category=legal; type=full; version=2023-04
```

5. Alternative Approaches

5.1. Page-specific approaches allow Tag Approach

**Approach tag usage example:**

```html

<meta name=“llms-source” content=“https://example.com/llms.txt” />

<meta name=“llms-source-full” content=“https://example.com/llms-full.txt” />

```

**Advantages**:

- Features and controls per page

- Easily embedded or implemented for website developers

- Section or content type specific for portions of the page or the entire page.

**Disadvantages**:

- Requires LLMs to crawl pages first before the meta tags can show which pages were visited

- Makes policies all over the site ineffective as every page would require a redundant tag option

- Extra burden on the page rendering.

- Cannot be viewed without parsing the html because it is contained within the html.

### 5.2. .well-known Resources Framework

**Example of Syntax**:

```
GET /.well-known/llms-source
> https://example.com/llms.txt
GET /.well-known/llms-policy
> {"policy": "structured-data", "formats": ["markdown", "json"]}
```

**Pros**:

- adheres to the established conventions for well-known resources,
- central point for resource discovery,
- does not need changes to existing robots.txt,
- structured formats (JSON, XML) can be used for complex policies.

**Cons**:

- additional HTTP requests,
- loss of coverage within existing crawler ecosystems,
- potential additional server configuration,
- not supported by automated Web-API.

## 6. Adoption Strategy

### 6.1 Users of a Website

1. Create structured content files in llms.txt and/or llms-full.txt
2. Insert LLMS statements into the robots.txt
3. Maintain up-to-date content
4. Analyze LLM involvement and modify when necessary.

### 6.2 LLM Services

1. Look at the robots.txt on a website
2. Get and obey the LLMS instructions, if available
3. Apply suitable rate control and caching policies
4. Attribute content source publicly

## 7. Implementation Challenges

### 7.1 Technical Challenges

- managing change over time to the standard,
- setting limits to and preventing abuse,
- standardizing the content format,
- how to handle contradicting directives

### 7.2 Adoption Challenges

- fostering sector consensus,
- developing adoption incentives,
- informing website users
- Mechanisms of verification and compliance

## 8: Comparison of the LLMS Directive and Other Options

| Attribute | LLMS Directive in robots.txt | Meta Tags | .well-known |
|-----------|------------------------------|-----------|------------|
| Discovery | Follows accepted crawler path | Needs page HTML to be parsed | Needs another request to be made |
| Granularity | Across the site with variables | Specific to a page | Across the site |
| Implementation | Edit one file | Edit multiple pages | Configure the server |
| Backward compatibility | Very Good | Fair | Fair |
| Visibility to site owners | Very Good | Very Good | Fair |
| Structured data support | By means of linking | Partial | Complete |
| Adoption barrier | Minimal | Medium | Higher than average |

## 9. Next Steps

1. Compile community reactions to this proposal.

2. Set an official specification for the parameters of the LLMS directive

3. Prepare widely used web platforms with reference implementations

4. Aquire collaboration with W3C or IETF standardization groups

5. Create tools that produce correct llms.txt files

6. Create guidelines that stipulate best practices for updating and formatting the content

## 10. Conclusion

It provides a modern SOLUTION to the problem of robots.txt for Large Language Models. The LLMS directive is a new approach to limiting the scope for the more advanced LLM chatbot functions.

This method benefits the entire web ecosystem by offering better control to website owners while simultaneously granting more efficient access to LLM providers.

I encourage all members of the community to help shape the development of this standard through feedback, suggestions, and participation in its ongoing evolution.

