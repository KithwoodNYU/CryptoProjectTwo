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
.nosoup {
    display: none;
}
#error {
    display:none;
    font-weight: bold;
    color: black;
    background: red;
}

.blurb {
    font-size: smaller;
    background: lightgrey;
    color: black;
    margin-left: 12px;
    padding: 4px;
    border-radius: 8px;
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
{%if a.id == 'a3q5' %}
<div class='nosoup'><a href="https://knowyourmeme.com/memes/no-soup-for-you-soup-nazi"><img src="/assets/images/nosoup.jpg"><br>We will assume the answer is false.</a></div>
{%endif%}
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
                if($(e).attr('id') == 'a3q5')
                    $(e).siblings('.nosoup').hide();
            });
        }).trigger('deselect');

       if($(this).data('subq') == '1' && $(this).is(":checked")) {
            $(this).siblings('.subquestion').show(400, 'swing'); // why does this not work?
        }

        if($(this).attr('id') == 'a3q5' && $(this).is(":checked")) {
            $(this).siblings('.nosoup').show();
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
            { "name":"DES", "id":"a1" , "url":"https://en.wikipedia.org/wiki/Data_Encryption_Standard", "blurb":"<strong>Data Encryption Standard</strong><br>16 Round Feistel Network with a block length of 64 and key length 56<br>Any small round variant of DES can be broken because of the small avalanche factor<br>Security - DES is considered relatively insecure due to the feasibility of brute force attacks.<br>In 1997, the DESCHALL Project broke a message encrypted with DES in 96 days.<br>In 2017, a chosen-plaintext attack utilizing a rainbow table was able to recover the DES key for a single chosen plaintext in 25 seconds.<br>Efficiency - Slower than AES"},
            { "name":"Triple DES", "id":"a2", "url":"https://en.wikipedia.org/wiki/Triple_DES", "blurb":"<strong>Triple DES</strong><br>Like its namesake, applies the DES cipher three times to each data block.<br>Triple DES has an effective key length of 168 bits (three 56 bit DES keys)<br>Security - Triple DES has a short block size of 64 bits, making it vulnerable to block collision attacks.<br>However, its not known to be efficiently breakable. Researchers were able to obtain a collision after 2^20 blocks which took 25 minutes<br>Efficiency - it’s slower than DES"},
            { "name":"AES in ECB mode", "id":"a3", "url":"http://www.cryptogrium.com/aes-encryption-online-ecb.html#:~:text=Electronic%20Codebook%20%28ECB%29%20mode%20is%20the%20simplest%20encryption,key%20size%20of%20128%2C%20192%20or%20256%20bits.", "blurb":"Advanced Encryption Standard<br>Based on a design principle known as a substitution-permutation network and is designed to be fairly efficient. It uses a block length of 128 bits and key sizes of 128, 192, or 256 bits.<br>Security - Not known to be efficiently breakable <br>Efficiency - Fast enough for almost all applications (except for resource-constrained devices)<br><strong>Electronic Code Book Mode</strong><br>Block<br>No initialization vector<br>Uses the same F on each message block and the encryption scheme is deterministic so it cannot satisfy IND-CPA<br>Efficiency - Fastest<br>Security - leaks block equality, may leak image<br>"},
            { "name":"AES in CBC mode", "id":"a4", "url":"https://tools.ietf.org/html/rfc3268", "blurb":"<strong>Advanced Encryption Standard</strong><br>Based on a design principle known as a substitution-permutation network and is designed to be fairly efficient. It uses a block length of 128 bits and key sizes of 128, 192, or 256 bits.<br>Security - Not known to be efficiently breakable <br>Efficiency - Fast enough for almost all applications (except for resource-constrained devices)<br><strong>Cipher Block Chaining Mode</strong><br>Block<br>Uses an initialization vector XOR-ed with the first message before going through the F<br>Drawback is that its inherently sequential<br>Efficiency - fast enough for almost all applications<br>Security - supports IND-CPA if cipher is a PRF"},
            { "name":"AES in OFB mode", "id":"a5", "url":"https://www.includehelp.com/cryptography/output-feedback-mode-ofb-in-cryptography.aspx", "blurb":"<strong>Advanced Encryption Standard</strong><br>Based on a design principle known as a substitution-permutation network and is designed to be fairly efficient. It uses a block length of 128 bits and key sizes of 128, 192, or 256 bits.<br>Security - Not known to be efficiently breakable <br>Efficiency - Fast enough for almost all applications (except for resource-constrained devices)<br><strong>Output Feedback Mode</strong><br>Can be used as a stream<br>Uses the IV as input through the F, taking the output and using it as input for the next block<br>The pseudo-random stream can be computed in a preprocessing phase (before m is available), allowing it to be used for streaming<br>Efficiency - fast enough for almost all applications<br>Security - supports IND-CPA if cipher is a PRF"},
            { "name":"AES in Counter (CTR) mode", "id":"a6", "url":"https://www.gurutechnologies.net/blog/aes-ctr-encryption-in-c/", "blurb":"<strong>Advanced Encryption Standard</strong><br>Based on a design principle known as a substitution-permutation network and is designed to be fairly efficient. It uses a block length of 128 bits and key sizes of 128, 192, or 256 bits.<br>Security - Not known to be efficiently breakable <br>Efficiency - Fast enough for almost all applications (except for resource-constrained devices)<br><strong>Counter Mode</strong><br>Can be used as a stream<br>Uses a random n-bit IV, denoted as a counter, and runs the input through the F. The counter iterates and is run through subsequent blocks<br>The pseudo-random stream can be computed in a preprocessing phase (before m is available), allowing it to be used for streaming<br>Efficiency - faster for almost all applications<br>Security - supports IND-CPA if cipher is a PRF"},
            { "name":"AES in GCM mode", "id":"a7", "url":"https://en.wikipedia.org/wiki/Galois/Counter_Mode", "blurb":"<strong>Advanced Encryption Standard</strong><br>Based on a design principle known as a substitution-permutation network and is designed to be fairly efficient. It uses a block length of 128 bits and key sizes of 128, 192, or 256 bits.<br>Security - Not known to be efficiently breakable <br>Efficiency - Fast enough for almost all applications (except for resource-constrained devices)<br><strong>Galois Counter Mode</strong><br>Can be used as a stream<br>Variant of Counter mode - using the Galois mode of authentication<br>Efficiency - faster for almost all applications<br>Security - supports IND-CPA if cipher is a PRF"},
            { "name":"IDEA", "id":"a8", "url":"https://en.wikipedia.org/wiki/International_Data_Encryption_Algorithm", "blurb":"<strong>International Data Encryption Algorithm</strong><br>IDEA is designed around a series of 8 identical transformation rounds and an output transformation using a 64 bit block and a 128 bit key<br>Security - Not known to be efficiently breakable. However, the simple key schedule makes IDEA subject to a class of weak keys (these were remedied by XORing each subkey with a 16-bit constant)<br>Efficiency - Fast enough for almost all applications (except for resource-constrained devices)<br>Patents expired and IDEA is free for all uses"},
            { "name":"SIMON", "id":"a9", "url":"https://en.wikipedia.org/wiki/Simon_%28cipher%29#:~:text=Description%20of%20the%20cipher%20The%20Simon%20block%20cipher,implementation%20is%20denoted%20as%20Simon2%20n%20%2F%20nm.", "blurb":"Simon is a family of lightweight block ciphers optimized for performance in hardware implementations<br>Simon is a balanced Feistel cipher with an n-bit word, and therefore the block length is 2n. The key length is a multiple of n by 2, 3, or 4, which is the value m. Therefore, a Simon cipher implementation is denoted as Simon2n/nm.<br>In practice, Simon can have block sizes from 32 to 128 bits, using key sizes of 64 to 256 bits and 32 to 72 rounds.<br>Security - breakable with large-computation attack especially on reduced round variants of Simon. The best published attacks on Simon involve differential cryptanalysis attacks and are only marginally faster than brute-force attacks<br>Efficiency - Faster than AES, targeting usage on resource-constrained devices"},
            { "name":"Twofish", "id":"a10", "url":"https://www.schneier.com/academic/twofish/", "blurb":"Twofish was an AES contest finalist and uses a Feistel network along with a series of precomputed key-dependent S-boxes and a complex key schedule<br>Twofish also uses a 128 bit block along with a 128, 192, or 256 bit key size and involves 16 rounds<br>Security - not known to be efficiently breakable. A cryptanalysis claims that it will take roughly 2^51 chosen plaintexts to find a good pair of truncated differentials<br>Efficiency - Significantly slower than AES<br>Not patented and has been placed in the public domain"},
            { "name":"RC4", "id":"a11", "url":"https://en.wikipedia.org/wiki/RC4", "blurb":"Rivest Cipher 4 is a stream cipher<br>The cipher was originally a trade secret but it uses a pseudorandom keystream, initialized with a variable length key using a key scheduling algorithm<br>Efficiency - highly efficient<br>Security - Its probably well known for its weaknesses in one of its implementations in WEP. The keystream generated by RC4 is biased towards certain sequences making it vulnerable to distinguishing attacks"},
            { "name":"RC5", "id":"a12", "url":"https://en.wikipedia.org/wiki/RC5", "blurb":"Rivest Cipher 5 is a symmetric key block cipher<br>The cipher uses data-dependent rotations in a Feistel-like network using block sizes of 32, 64, or 128 bits, key sizes between 0 and 2040 bits, and 1-255 rounds.<br>Efficiency - efficient due to general structure of the algorithm<br>Security - not efficiently breakable. Researchers have been able to perform a differential attack against a 12-round RC5 with 64 bit blocks using 2^44 chosen plaintexts. Accordingly, the developers claim that 18-20 rounds are sufficient protection<br>RSA has a patent on the algorithm"},
            { "name":"RC6", "id":"a13", "url":"https://en.wikipedia.org/wiki/RC6", "blurb":"Rivest Cipher 6 is a symmetric key block cipher and AES finalist<br>The cipher is similar to RC5, uses data dependent rotations, modular addition, and XOR operations; its been likened to interweaving two parallel RC5 encryption processes<br>RC6 uses block sizes of 128 bits, key sizes of 128, 192, or 256 bits, and 20 rounds<br>Efficiency - less than RC5<br>Security- not efficiently breakable<br>Patents expired and possibly royalty free"},
            { "name":"One Time Pad", "id":"a14", "url":"https://en.wikipedia.org/wiki/One-time_pad", "blurb":"One Time Pad<br>In order to be a true OTP: 1) the key must be truly random (not pseudorandom); 2) the key must be as long if not longer than the plaintext; 3) they key cannot be reused in whole or in part; and 4) the key must be kept secret.<br>When those conditions are met, the OTP maintains the property of perfect secrecy<br>Efficiency - generating true randomness for the key is highly inefficient but the XOR operation against the message is extremely efficient"},
            { "name":"No soup for you!", "id":"a15", "url":"https://knowyourmeme.com/memes/no-soup-for-you-soup-nazi"}
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
        if(scheme && scheme['id'] != 'a15'){
            htmlstr += "<li><a href='" + scheme['url'] + "'>" + scheme['name'] + "</a><div class='blurb'>" + scheme['blurb'] + "</div></li>";
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
    $('.nosoup').hide();
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
