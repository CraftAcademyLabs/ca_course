**Here is a summary of the most common Capybara commands**

```
=Navigating=
    visit('/projects')
    visit(post_comments_path(post))

=Clicking links and buttons=
    click_link('id-of-link')
    click_link('Link Text')
    click_button('Save')
    click('Link Text') # Click either a link or a button
    click('Button Value')
    click_link_or_button('Link Text') # Click either a link or a button


=Interacting with forms=
    fill_in('First Name', with: 'John')
    fill_in('Password', with: 'Seekrit')
    fill_in('Description', with: 'Really Long Text…')
    choose('A Radio Button')
    check('A Checkbox')
    uncheck('A Checkbox')
    attach_file('Image', '/path/to/image.jpg')
    select('Option', from: 'Select Box')

=scoping=
    within("//li[@id='employee']") do
      fill_in 'Name', with: 'Thomas'
    end
    within(:css, "li#employee") do
      fill_in 'Name', with: 'Thomas'
    end
    within_fieldset('Employee') do
      fill_in 'Name', with: 'Thomas'
    end
    within_table('Employee') do
      fill_in 'Name', with: 'Thomas'
    end

=Querying=

    expect(page).to have_xpath('//table/tr')
    expect(page).to have_css('table tr.foo')
    expect(page).to have_content('foo')
    expect(page).not_to have_content('foo')
    find_field('First Name').value
    find_link('Hello').visible?
    find_button('Send').click
    find('//table/tr').click
    locate("//*[@id='overlay'").find("//h1").click
    all('a').each { |a| a[:href] }

=Scripting=
    result = page.evaluate_script('4 + 4');

=Debugging=
    save_and_open_page```
