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
This tool is provided to help analyze what sort of encryption scheme you should use for your private key encryption.

* Answer the questions to the best of your ability
* Click Continue to go to the next set of questions
* Click Back to revisit answered questions
* Click Show me the Results to see your chosen schemes

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
<div>
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
{% endfor %} <!-- for a in subq.answers -->
{% endif %} <!-- if subq.type == 'singlechoice' -->
{% if subq.type == 'dropdown' %}
<select id="{{a.id}}" name="{{q.qid}}">
{% for a in subq.answers %}
<option value="{{a.a}}">{{a.a}}</option>
{% endfor %} <!-- for a in subq.answers -->
</select><br>
{% endif %} <!-- if subq.type == 'dropdown' -->
<br>
{% endfor %} <!-- for subq in a.questions -->
</section>
{% endif %} <!-- if a.questions.size > 0 -->
</div>
{% endfor %} <!-- for a in q.answers -->

{% endif %} <!-- if q.type == 'singlechoice' or q.type == 'multichoice' -->

{% if q.type == 'dropdown' %}
<select id="{{a.id}}" name="{{q.qid}}">
{% for a in q.answers %}
<div>
<option value="{{a.a}}">{{a.a}}</option>
{% endfor %}
</select><br>
{% endif %}

<br>
{% endfor %}
</section>

{% endfor %}
<button id='prevSection'>Back</button><button id='nextSection'>Continue</button><button id='showAnswers'>Show me the Answers</button>


<script type='text/javascript'>
var on_section = 's1';
var sections = ['s1','s2','s3'];   
var all_answers = {
        "a1q4": [ "a1","a2","a3","a4","a5","a6","a7","a8","a9","a10","a11","a12","a13" ],
        "a2q4":  [ "a4","a5","a6","a7","a8","a9","a10","a11","a12","a13","a14"],
        "a3q4":  [ "a4","a5","a6","a7","a8","a9","a10","a11","a12","a13" ],
        "a1q1": ["a1","a2","a3","a4","a5","a6","a7","a9","a10","a12","a13","a14"],
        "a2q1": ["a1","a2","a3","a4","a5","a6","a7","a8","a9","a10","a11","a12","a13","a14"],
        "a1q2": ["a1","a2","a11"],
        "a2q2": ["a1","a2","a3","a4","a5","a6","a7","a8","a9","a10","a11","a12","a13","a14"],
        "a1q3": ["a1","a2","a3","a11"],
        "a2q3": ["a1","a2","a3","a4","a5","a6","a7","a8","a9","a10","a11","a12","a13","a14"],
        "a1q5": ["a4","a5","a6","a7","a10","a13","a14"],
        "a2q5": ["a1","a2","a3","a4","a5","a6","a7","a8","a9","a10","a11","a12","a13"],
        "a3q5": ["a15"],
        "a1q6": ["a5","a6","a7","a11","a14"],
        "a2q6": ["a1","a2","a3","a4","a5","a6","a7","a8","a9","a10","a11","a12","a13","a14"],
        "a3q6": ["a3","a4","a5","a6","a7","a8","a9","a10","a11","a12","a13","a14"],
        "a1q7": ["a1","a3","a11"],
        "a2q7": ["a2","a8","a9","a12"],
        "a3q7": ["a4","a5","a6","a7","a10","a13","a14"]
        };
var chosen_answers = ["a1","a2","a3","a4","a5","a6","a7","a8","a9","a10","a11","a12","a13","a14","a15"];
var running_answers = [["a1","a2","a3","a4","a5","a6","a7","a8","a9","a10","a11","a12","a13","a14","a15"]];

$(function () {
    $('#prevSection').hide();
    $('#showAnswers').hide();

   updateSections(true);

   $('#nextSection').click(function() {
        ndx = sections.indexOf(on_section);
        on_section = sections[ndx + 1];

        $(':radio').each(function () {
            if($(this).attr('display') != 'none' && $(this).is(":checked") && !$(this).data('subq') == '1'){
                addAnswers(all_answers[$(this).attr('id')]);
            }
        });
        
        $(':checkbox').each(function () {
            if($(this).visible && $(this).is(":checked") && !$(this).data('subq') == '1'){
                addAnswers(all_answers[$(this).attr('id')]);                
            }
        });
        //alert(chosen_answers);
        running_answers.push(chosen_answers);
        updateSections();
        if(on_section == sections[sections.length - 1])
        {
            $('#nextSection').hide();
            $('#showAnswers').show();
        }

        $('#prevSection').show();
   });

   $('#prevSection').click(function() {
       ndx = sections.indexOf(on_section);
       if(ndx > 0)
          on_section = sections[ndx - 1];

        running_answers.pop();
        chosen_answers = running_answers[running_answers.length - 1];
        //alert(chosen_answers);
        //alert(running_answers.length);
        updateSections();
        if(on_section == sections[0])
            $('#prevSection').hide();
        else {
            $('#nextSection').show();
            $('#showAnswers').hide();
        }
   });

   $('#showAnswers').click(function() {
       alert(chosen_answers);
   })

   $(':radio').click(function () {
       var groupname = $(this).attr('name');
       $("input[name^='" + groupname + "']").not(':checked').off('deselect').on('deselect', function() {
            $(this).each(function(i, e) {
                $(e).siblings('.subquestion').hide(400, 'swing');
            });
        }).trigger('deselect');

       if($(this).data('subq') == '1' && $(this).is(":checked")) {
            $(this).siblings('.subquestion').show(400, 'swing'); // why does this not work?
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

function addAnswers(answers) {
    if(running_answers.length <= 0) {
        chosen_answers = answers;
    }
    else {
        for(let ans in chosen_answers){
            if(answers.indexOf(chosen_answers[ans]) < 0){
                chosen_answers.splice(ans, 1);
            }
        }                
    }
}


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
