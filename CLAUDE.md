# JSON Resume Theme Development Guide

This repository contains the **Macchiato** JSON Resume theme, a Handlebars-based theme for generating HTML resumes from JSON Resume format.

## Architecture Overview

- **Main Entry Point**: `index.js` - exports the required `render` function
- **Templating**: Handlebars with partials for modular components
- **Styling**: Inline CSS with embedded fonts for complete portability
- **Testing**: Visual regression testing with Puppeteer and Jest

## Key Commands

### Development
```bash
npm test                    # Run visual regression tests
npm run pretty             # Format code with Prettier
npm run lint               # Run ESLint
npm run prepublishOnly     # Pre-publish validation (format, lint, test)
```

### Testing JSON Resume Locally
```bash
# Install resume-cli globally if needed
npm install -g resume-cli

# Test theme with sample resume
resume export resume.html --theme .

# Debug with verbose output
resume export resume.html --theme . --debug

# Generate PDF from HTML (requires puppeteer-cli)
npm install -g puppeteer-cli
puppeteer --wait-until networkidle0 --margin-top 0 --margin-right 0 --margin-bottom 0 --margin-left 0 --format A4 print resume.html resume.pdf
```

## File Structure

- `/src/resume.hbs` - Main HTML template
- `/src/style.css` - Complete CSS styling (inlined in output)
- `/src/partials/` - Handlebars partials for each resume section:
  - `resume-header.hbs` - Header with name/contact info
  - `work.hbs` - Work experience
  - `education.hbs` - Education history
  - `skills.hbs` - Skills with tags
  - `projects.hbs` - Projects with keywords/tags
  - And more...

## JSON Resume Requirements

1. **Export Function**: Must export a `render` function that:
   - Takes a JSON resume object as input
   - Returns complete HTML string
   - Is "pure" with no side effects
   - Inlines all CSS and assets

2. **Naming Convention**: NPM package must be named `jsonresume-theme-{name}`

3. **Self-Contained**: All assets must be embedded (CSS, fonts, images as base64)

## Development Best Practices

### When Modifying Templates
- Always test with `npm test` to ensure visual output remains consistent
- Use the sample `resume.json` for development testing
- Check both HTML export and PDF generation
- Ensure proper responsive design for different screen sizes

### When Adding New Sections
1. Create partial in `/src/partials/`
2. Add to main template in `/src/resume.hbs`
3. Style in `/src/style.css` 
4. Test with sample data in `resume.json`
5. Run visual regression tests

### Handlebars Helpers Available
- `removeProtocol` - Remove protocol from URLs
- `concat` - Concatenate strings
- `formatAddress` - Format address components
- `formatDate` - Format dates as MM/YYYY
- `lowercase` - Convert to lowercase
- `eq` - Equality comparison

### CSS Guidelines
- Use CSS variables for consistent theming
- Maintain print-friendly styles
- Ensure proper font loading with @font-face
- Keep all styles in single `style.css` file
- Follow existing class naming conventions

### Testing Strategy
- Visual regression testing with Puppeteer screenshots
- Test both screen and print layouts
- Verify with different resume data structures
- Check Font Awesome icons render correctly

## Theme Customization

### Color Scheme
Primary colors defined in CSS variables at top of `style.css`

### Typography
- Headers: Josefin Sans
- Body text: Lato
- Icons: Font Awesome 4.7.0

### Layout
- Two-column responsive layout
- Left sidebar: About, Skills, Languages, Interests
- Right column: Summary, Work, Projects, Education, etc.

## Publishing Workflow

1. Make changes and test locally
2. Run `npm run prepublishOnly` to validate
3. Update version in `package.json`
4. Update `CHANGELOG.md`
5. Commit changes
6. `npm publish` to NPM registry

## Common Tasks

### Add New Resume Section
```bash
# 1. Create partial template
touch src/partials/newsection.hbs

# 2. Add handlebars template content for new section
# 3. Include in main template: {{> newsection}}
# 4. Add CSS styling for .newsection-container
# 5. Test with sample data
npm test
```

### Modify Styling
```bash
# 1. Edit src/style.css
# 2. Test visual changes
npm test

# 3. If tests fail, review image diff and update snapshots if correct
npm test -- --updateSnapshot
```

### Common Issues
1. **Theme not found**: Ensure theme follows `jsonresume-theme-{name}` NPM convention
2. **Assets not loading**: Verify all external resources are embedded/inlined
3. **Template errors**: Validate Handlebars syntax and data structure assumptions  
4. **PDF generation issues**: Check CSS print styles and page break handling

### Debug Template Issues
- Use `resume export resume.html --theme .` to generate test HTML
- Use `resume export resume.html --theme . --debug` for verbose output
- Open generated HTML in browser to debug rendering
- Check browser console for any JavaScript/CSS errors
- Validate Handlebars template syntax

## Dependencies

### Runtime Dependencies
- `handlebars` - Template engine
- `handlebars-wax` - Handlebars extensions
- `moment` - Date formatting
- `address-format` - Address formatting (currently unused)

### Development Dependencies
- `jest` + `jest-image-snapshot` - Visual regression testing
- `puppeteer` - Headless browser for testing
- `eslint` - Code linting
- `prettier` - Code formatting

## JSON Resume Core Requirements

### Theme Must Export
- A `render` function that takes JSON resume object and returns HTML string
- Function must be "pure" with no side effects
- Cannot use Node.js modules like `fs` or `http` in runtime (build-time OK)

### Self-Contained Output
- All CSS must be inlined in HTML output
- All fonts and assets must be embedded or base64 encoded
- No external dependencies in final HTML

### NPM Package Requirements
- Package name: `jsonresume-theme-{name}` convention
- Main field in package.json must point to entry file
- Must export render function correctly

## Publishing Checklist

- [ ] Package name follows `jsonresume-theme-{name}` convention
- [ ] Render function exports correctly and returns complete HTML
- [ ] All assets are embedded/inlined in output
- [ ] Works with `resume export --theme .`
- [ ] PDF generation produces good output
- [ ] Visual regression tests pass
- [ ] No runtime external dependencies

## Notes

- This theme is based on the Caffeine theme but with a simpler development environment
- All fonts and external assets are embedded for complete portability
- Visual regression tests ensure consistency across changes
- Theme follows JSON Resume schema conventions