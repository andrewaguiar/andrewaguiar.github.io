---
layout: post
title: "Ruby - Quick capybaha cheat sheet"
image: "capybaha"
---

## Navigation
```ruby
visit '/'
```

## Clicking
```ruby
click 'text'
click_link 'id|text'
click_button 'Save'
click_on 'link text|button value'
```

## Forms
```ruby
fill_in 'id', with: 'Text'
choose 'radio'
check 'checkbox'
uncheck 'checkbox'
attach_file 'Image', '/path/to/file.jpg'
select 'option', from: 'select element'
unselect
```

# Verifying
```ruby
expect(page).to have_selector
expect(page).to have_button
expect(page).to have_checked_field
expect(page).to have_css
expect(page).to have_field 
expect(page).to have_link
expect(page).to have_select
expect(page).to have_table
expect(page).to have_text
expect(page).to have_unchecked_field
expect(page).to have_xpath
```

## Page
```ruby
page.all 'a'
page.body
page.html
page.source
page.current_host
page.current_path
page.current_url
page.status_code
page.response_headers
```

## Debugging
```ruby
save_and_open_page # Saves a html file with the page content rendered.
```

## References
  - [Capybara github page](https://github.com/jnicklas/capybara)
  - [Capybara documentation](http://www.rubydoc.info/github/jnicklas/capybara/)
