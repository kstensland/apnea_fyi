---
title: "Contact"
permalink: "/contact/"
layout: page
# Text and form labels like the menu's small links (_sass/menu.sass).
# (Every page title is styled like the menu's.)
article_class: menu-text
---

{%- comment -%}
  The form is sent by FormSubmit (https://formsubmit.co), since GitHub Pages
  can't send email. FormSubmit emails each message to the address in the
  form's action. The very first message sends an "activate this form" email to
  that inbox instead; someone must click it once before messages come through.
  After a message is sent, FormSubmit shows a short check (a captcha) and then
  returns the visitor to /contact/thanks/.
{%- endcomment -%}

Questions about competing, volunteering or anything else? Send us a message and we'll reply by email.

<form class="contact-form" action="https://formsubmit.co/emeraldcityapneachallenge@gmail.com" method="POST">
  <!-- FormSubmit settings -->
  <input type="hidden" name="_subject" value="New message from apnea.fyi">
  <input type="hidden" name="_template" value="table">
  <input type="hidden" name="_next" value="{{ '/contact/thanks/' | absolute_url }}">
  <!-- Left empty by people; bots that fill it in are ignored. -->
  <input type="text" name="_honey" class="contact-honey" tabindex="-1" autocomplete="off" aria-hidden="true">

  <label for="contact-name">Name</label>
  <input id="contact-name" type="text" name="name" autocomplete="name" required>

  <!-- FormSubmit uses the "email" field as the reply-to address. -->
  <label for="contact-email">Email</label>
  <input id="contact-email" type="email" name="email" autocomplete="email" required>

  <label for="contact-message">Message</label>
  <textarea id="contact-message" name="message" rows="7" required></textarea>

  <button type="submit" class="contact-submit outline-button">Send message</button>
</form>

<p class="contact-alt">Or email us directly at <a href="mailto:emeraldcityapneachallenge@gmail.com">emeraldcityapneachallenge@gmail.com</a>.</p>
