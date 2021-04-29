---

title: SH4D3S CryptoQuery

---
<style>
button {
    margin: 24px;
    background: purple;
    color: white;
    min-width: 125px;
}

section {
    margin-bottom: 24px;
    display: none;
}

.subquestion {
    margin-left: 45px;
    display: none;
    margin-bottom: 0px;
}
</style>
{% include addons.html %}

![header image](/assets/images/sh4d3s_cp2.png)


## Instructions
* Do this and then
* Do that and then
* Answer answer answer


## Questions

{% for s in site.data.cryptoqueries %}
<section id="{{s.secid}}">
<h3>{{ s.section }}</h3>


{% assign inputtype = 'radio' %}
{% for q in s.questions %}
{% if q.type == 'multichoice' %}
{% assign inputtype = 'checkbox' %}
{% endif %}
<strong>Q: {{ q.q }}</strong><br>
{% if q.type == 'singlechoice' or q.type == 'multichoice' %}
{% for a in q.answers %}
<input type='{{ inputtype }}' id="{{a.id}}" name="{{q.qid}}" value="{{a.a}}" data-subq="{{a.questions.size}}"/>
<label for="{{a.id}}">{{a.a}}</label><br>

{% if a.questions.size > 0 %}
<section class='subquestion'>
{% for subq in a.questions %}
<strong>Q: {{ subq.q }}</strong><br>
{% if subq.type == 'singlechoice' %}
{% for a in subq.answers %}
<input type='radio' id="{{a.id}}" name="{{subq.qid}}" value="{{a.a}}"/>
<label for="{{a.id}}">{{a.a}}</label><br>
{% endfor %}
{% endif %}
{% if subq.type == 'dropdown' %}
<select id="{{a.id}}" name="{{q.qid}}">
{% for a in subq.answers %}
<option value="{{a.a}}">{{a.a}}</option>
{% endfor %}
</select><br>
{% endif %}
<br>
{% endfor %}
</section>
{% endif %}

{% endfor %}

{% endif %}

{% if q.type == 'dropdown' %}
<select id="{{a.id}}" name="{{q.qid}}">
{% for a in q.answers %}
<option value="{{a.a}}">{{a.a}}</option>
{% endfor %}
</select><br>
{% endif %}

<br>

{% endfor %}
</section>

{% endfor %}

<button id='prevSection'>Back</button><button id='nextSection'>Continue</button>


<script type='text/javascript'>
var on_section = 's1';
var sections = ['s1','s2','s3'];   

$(function () {
    $('#prevSection').hide();
   updateSections(true);

   $('#nextSection').click(function() {
        ndx = sections.indexOf(on_section);
        on_section = sections[ndx + 1];
        updateSections();
        if(on_section == sections[sections.length - 1])
            $('#nextSection').hide();
        
        $('#prevSection').show();
   });

   $('#prevSection').click(function() {
       ndx = sections.indexOf(on_section);
       if(ndx > 0)
          on_section = sections[ndx - 1];

        updateSections();
        if(on_section == sections[0])
            $('#prevSection').hide();
        else {
            $('#nextSection').show();
        }
   });

   $(':radio').click(function() {
        if($(this).data('subq') == '1') {
            $(this).siblings('.subquestion').show(400, 'swing'); // why does this not work?
        }
        else {
            $(this).siblings('.subquestion').hide(400, 'swing'); // why does this not work?
        }
   });

   $(':checkbox').click(function() {
        if($(this).data('subq') == '1') {
            if($(this).is(":not(:checked)"))
                $(this).siblings('.subquestion').hide(400, 'swing'); 
            else
                $(this).siblings('.subquestion').show(400, 'swing'); 
        }
        
   });

});

function updateSections(initial = false) {
    $('section').each(function () {
       if($(this).attr('id') != on_section) {
           if(initial){           
                $(this).hide();
           }
           else if($(this).attr('class') != 'subquestion')
                $(this).hide(400, 'swing');
       }
       else {
           $(this).show(400, 'swing');
       }
   });
};
</script>
