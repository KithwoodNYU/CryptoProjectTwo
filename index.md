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

#error {
    display:none;
    font-weight: bold;
    color: black;
    background: red;
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

<div id='error'>Please answer all questions in this section to continue</div>
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

<section id='final'>

</section>

<button id='prevSection'>Back</button><button id='nextSection'>Continue</button><button id='showAnswers'>Show me the Answers</button><button id='startOver'>Start Over</button>


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

    init();
   

   $('#nextSection').click(function() {
       $('#error').hide();
        add_answers = [];
        qcount = 0;
        acount = 0;
        $(':radio').each(function () {
            if($(this).is(':visible') && $(this).is(":checked") && $(this).data('subq') != '1'){
                add_answers.push(all_answers[$(this).attr('id')]);
            }

            if($(this).is(':visible') && $(this).is(':checked')) {
                acount +=1;
            }
        });
        
        $(':checkbox').each(function () {
            if($(this).is(':visible') && $(this).is(":checked") && $(this).data('subq') != '1'){
                add_answers.push(all_answers[$(this).attr('id')]);                
            }
            if($(this).is(':visible') && $(this).is(':checked')) {
                acount +=1;
            }
        });
       
       $('strong').each(function(){
           
           if($(this).is(':visible'))
                qcount += 1;                
       });
       
       alert('answers' + acount);
       alert('questions' + qcount);

        if(acount < qcount) {
            $('#error').show();
            return;
        }

        for(let ans in add_answers) {
            addAnswers(add_answers[ans]);
        }

        ndx = sections.indexOf(on_section);
        on_section = sections[ndx + 1];
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
       $('#error').hide();
        add_answers = [];
        qcount = 0;
        acount = 0;
        $(':radio').each(function () {
            if($(this).is(':visible') && $(this).is(":checked") && !$(this).data('subq') == '1'){
                add_answers.push(all_answers[$(this).attr('id')]);
                acount += 1;
            }
        });
        
        $(':checkbox').each(function () {
            if($(this).is(":visible") && $(this).is(":checked") && !$(this).data('subq') == '1'){
                add_answers.push(all_answers[$(this).attr('id')]);     
                acount += 1;           
            }
        });
        $('strong').each(function(){
           
           if($(this).is(':visible'))
                qcount += 1;                
       });
       
        if(acount < qcount) {
            $('#error').show();
            return;
        }
        for(let ans in add_answers) {
            addAnswers(add_answers[ans]);
        }
        
        running_answers.push(chosen_answers);
        goto_answers();
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

    $('#startOver').click(function() {
        init();
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

function getscheme(scheme_id) {
    var scheme = {}
    var schemes = { "schemes": [
            { "name":"DES", "id":"a1" , "url":"https://en.wikipedia.org/wiki/Data_Encryption_Standard"},
            { "name":"Triple DES", "id":"a2", "url":"https://en.wikipedia.org/wiki/Triple_DES"},
            { "name":"AES in ECB mode", "id":"a3", "url":"http://www.cryptogrium.com/aes-encryption-online-ecb.html#:~:text=Electronic%20Codebook%20%28ECB%29%20mode%20is%20the%20simplest%20encryption,key%20size%20of%20128%2C%20192%20or%20256%20bits."},
            { "name":"AES in CBC mode", "id":"a4", "url":"https://tools.ietf.org/html/rfc3268"},
            { "name":"AES in OFB mode", "id":"a5", "url":"https://www.includehelp.com/cryptography/output-feedback-mode-ofb-in-cryptography.aspx"},
            { "name":"AES in Counter (CTR) mode", "id":"a6", "url":"https://www.gurutechnologies.net/blog/aes-ctr-encryption-in-c/"},
            { "name":"AES in GCM mode", "id":"a7", "url":"https://en.wikipedia.org/wiki/Galois/Counter_Mode"},
            { "name":"IDEA", "id":"a8", "url":"https://en.wikipedia.org/wiki/International_Data_Encryption_Algorithm"},
            { "name":"SIMON", "id":"a9", "url":"https://en.wikipedia.org/wiki/Simon_%28cipher%29#:~:text=Description%20of%20the%20cipher%20The%20Simon%20block%20cipher,implementation%20is%20denoted%20as%20Simon2%20n%20%2F%20nm."},
            { "name":"Twofish", "id":"a10", "url":"https://www.schneier.com/academic/twofish/"},
            { "name":"RC4", "id":"a11", "url":"https://en.wikipedia.org/wiki/RC4"},
            { "name":"RC5", "id":"a12", "url":"https://en.wikipedia.org/wiki/RC5"},
            { "name":"RC6", "id":"a13", "url":"https://en.wikipedia.org/wiki/RC6"},
            { "name":"One Time Pad", "id":"a14", "url":"https://en.wikipedia.org/wiki/One-time_pad"},
            { "name":"No soup for you!  We will assume no, continue...", "id":"a15", "url":"https://knowyourmeme.com/memes/no-soup-for-you-soup-nazi"}
        ]};

    for(let sch in schemes['schemes']) {

        if(schemes['schemes'][sch]['id'] == scheme_id)
            return schemes['schemes'][sch];
    }
    return scheme;
}
function goto_answers() {
    htmlstr = "After evaluating your answers, the cyrptographic schemes you should pursue are:<p>";
    htmlstr += "<ul>";
    
    for(let ans in chosen_answers) {
        scheme = getscheme(chosen_answers[ans]);
        if(scheme){
            htmlstr += "<li><a href='" + scheme['url'] + "'>" + scheme['name'] + "</a></li>";
        }
    }
    htmlstr += "</ul>";

    $('#final').html(htmlstr);
    $('section').each(function () {
       if($(this).attr('id') != 'final') {
          $(this).hide(400, 'swing');           
       }
   });
    $('#final').show();
    $('#startOver').show();
    $('#prevSection').hide();
    $('#showAnswers').hide();
}

function init() {
    on_section = 's1';
    updateSections(true);
    $('#prevSection').hide();
    $('#showAnswers').hide();
    $('#startOver').hide();
    $('#final').hide();
    $('#nextSection').show();
    $(':radio').each(function() {
        $(this).prop('checked', false);
    });
    $(':checkbox').each(function() {
        $(this).prop('checked', false);
    });

    chosen_answers = ["a1","a2","a3","a4","a5","a6","a7","a8","a9","a10","a11","a12","a13","a14","a15"];
    running_answers = [["a1","a2","a3","a4","a5","a6","a7","a8","a9","a10","a11","a12","a13","a14","a15"]];
}
</script>
