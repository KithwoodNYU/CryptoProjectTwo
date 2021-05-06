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
<div class='nosoup'><a href="https://knowyourmeme.com/memes/no-soup-for-you-soup-nazi" target="_blank"><img src="assets/images/nosoup.jpg"><br>We will assume the answer is false.</a></div>
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
        "a3q5": ["a1","a2","a3","a4","a5","a6","a7","a8","a9","a10","a11","a12","a13"],
        "a1q6": ["a5","a6","a7","a11","a14"],
        "a2q6": ["a1","a2","a3", "a4", "a5","a8","a9","a10","a12","a13","a14"],
        "a3q6": ["a3","a4", "a5","a8","a9","a10","a12","a13","a14"],
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
            { "name":"DES", "id":"a1" , "url":"https://en.wikipedia.org/wiki/Data_Encryption_Standard", "blurb":"<i>A symmetric-key block cipher designed by IBM in the early 1970s. It has since been replaced by more secure algorithms.</i><br><strong>Key Sizes:</strong> 56 bits<br><strong>Block Sizes:</strong> 64 bits<br><strong>Structure:</strong> Feistel Network<br><strong>Rounds:</strong> 16<br><strong>Security:</strong> Any small round variant of DES can be broken because of the small avalanche factor. DES is also considered relatively insecure due to the feasibility of brute force attacks. In 1997, researchers broke a message encrypted with DES in 96 days. In 2017, a chosen-plaintext attack utilizing a rainbow table was able to recover the DES key for a single chosen plaintext in 25 seconds.<br><strong>Efficiency:</strong> Slower than AES"},
            { "name":"Triple DES", "id":"a2", "url":"https://en.wikipedia.org/wiki/Triple_DES", "blurb":"<i>Triple DES, like its namesake, applies the DES cipher three times to each data block. While not as efficient as later algorithms, it is still used among certain industries and libraries. Recent guidelines propose that usage of Triple DES be disallowed after 2023.</i><br><strong>Key Sizes:</strong> 168 bits (56 x 3)<br><strong>Block Sizes:</strong> 64 bits<br><strong>Structure:</strong> Feistel Network<br><strong>Rounds:</strong> 48 (16 x 3)<br><strong>Security:</strong>Triple DES has a short block size of 64 bits, making it vulnerable to block collision attacks. However, its not known to be efficiently breakable. Researchers were able to obtain a collision after 2^20 blocks which took 25 minutes<br><strong>Efficiency:</strong> Slower than DES"},
            { "name":"AES in ECB mode", "id":"a3", "url":"http://www.cryptogrium.com/aes-encryption-online-ecb.html#:~:text=Electronic%20Codebook%20%28ECB%29%20mode%20is%20the%20simplest%20encryption,key%20size%20of%20128%2C%20192%20or%20256%20bits.", "blurb":"<strong>Advanced Encryption Standard</strong><br><i>The successor to DES, AES is widely adopted and supported symmetric algorithm significantly faster than DES and Triple DES. AES is also the U.S. Federal Information Processing Standard and has been approved by the National Security Agency for top secret information.</i><br><strong>Key Sizes:</strong> 128, 192, or 256 bits<br><strong>Block Sizes:</strong> 128 bits<br><strong>Structure:</strong> Substitution-Permutation Network<br><strong>Rounds:</strong> 10, 12, or 14<br><strong>Security:</strong> Not known to be efficiently breakable<br><strong>Efficiency:</strong> Fast enough for almost all applications (except for resource-constrained devices)<br><br><strong>Electronic Code Book Mode</strong><br><i>Block Cipher</i><br><strong>Details:</strong> No initialization vector. ECB Mode uses the same function on each message block and the encryption scheme is deterministic so it cannot satisfy IND-CPA<br><strong>Security:</strong> ECB Mode leaks block equality and may leak images<br><strong>Efficiency:</strong> Fastest"},
            { "name":"AES in CBC mode", "id":"a4", "url":"https://tools.ietf.org/html/rfc3268", "blurb":"<strong>Advanced Encryption Standard</strong><br><i>The successor to DES, AES is widely adopted and supported symmetric algorithm significantly faster than DES and Triple DES. AES is also the U.S. Federal Information Processing Standard and has been approved by the National Security Agency for top secret information.</i><br><strong>Key Sizes:</strong> 128, 192, or 256 bits<br><strong>Block Sizes:</strong> 128 bits<br><strong>Structure:</strong> Substitution-Permutation Network<br><strong>Rounds:</strong> 10, 12, or 14<br><strong>Security:</strong> Not known to be efficiently breakable<br><strong>Efficiency:</strong> Fast enough for almost all applications (except for resource-constrained devices)<br><br><strong>Cipher Block Chaining Mode</strong><br><i>Block Cipher</i><br><strong>Details:</strong> Uses an initialization vector XOR-ed with the first message before going through the function. The drawback to CBC Mode is that its inherently sequential<br><strong>Security:</strong> Supports IND-CPA if cipher is a PRF<br><strong>Efficiency:</strong> Fast enough for almost all applications"},
            { "name":"AES in OFB mode", "id":"a5", "url":"https://www.includehelp.com/cryptography/output-feedback-mode-ofb-in-cryptography.aspx", "blurb":"<strong>Advanced Encryption Standard</strong><br><i>The successor to DES, AES is widely adopted and supported symmetric algorithm significantly faster than DES and Triple DES. AES is also the U.S. Federal Information Processing Standard and has been approved by the National Security Agency for top secret information.</i><br><strong>Key Sizes:</strong> 128, 192, or 256 bits<br><strong>Block Sizes:</strong> 128 bits<br><strong>Structure:</strong> Substitution-Permutation Network<br><strong>Rounds:</strong> 10, 12, or 14<br><strong>Security:</strong> Not known to be efficiently breakable<br><strong>Efficiency:</strong> Fast enough for almost all applications (except for resource-constrained devices)<br><br><strong>Output Feedback Mode</strong><br>Can be used as a Stream Cipher<br><strong>Details:</strong> Uses the initialization vector as input through the function, taking the output and using it as input for the next block. The pseudo-random stream can be computed in a preprocessing phase (before the message is available), allowing it to be used for streaming<br><strong>Security:</strong> Supports IND-CPA if cipher is a PRF<br><strong>Efficiency:</strong> Fast enough for almost all applications"},
            { "name":"AES in Counter (CTR) mode", "id":"a6", "url":"https://www.gurutechnologies.net/blog/aes-ctr-encryption-in-c/", "blurb":"<strong>Advanced Encryption Standard</strong><br><i>The successor to DES, AES is widely adopted and supported symmetric algorithm significantly faster than DES and Triple DES. AES is also the U.S. Federal Information Processing Standard and has been approved by the National Security Agency for top secret information.</i><br><strong>Key Sizes:</strong> 128, 192, or 256 bits<br><strong>Block Sizes:</strong> 128 bits<br><strong>Structure:</strong> Substitution-Permutation Network<br><strong>Rounds:</strong> 10, 12, or 14<br><strong>Security:</strong> Not known to be efficiently breakable<br><strong>Efficiency:</strong> Fast enough for almost all applications (except for resource-constrained devices)<br><br><strong>Counter Mode</strong><i>Can be used as a Stream Cipher</i><br><strong>Details:</strong> Uses a random n-bit initialization vector, denoted as a counter, and runs the input through the function. The counter iterates and is run through subsequent blocks. The pseudo-random stream can be computed in a preprocessing phase (before the message is available), allowing it to be used for streaming<br><strong>Security:</strong> Supports IND-CPA if cipher is a PRF<br><strong>Efficiency:</strong> Faster for almost all applications"},
            { "name":"AES in GCM mode", "id":"a7", "url":"https://en.wikipedia.org/wiki/Galois/Counter_Mode", "blurb":"<strong>Advanced Encryption Standard</strong><br><i>The successor to DES, AES is widely adopted and supported symmetric algorithm significantly faster than DES and Triple DES. AES is also the U.S. Federal Information Processing Standard and has been approved by the National Security Agency for top secret information.</i><br><strong>Key Sizes:</strong> 128, 192, or 256 bits<br><strong>Block Sizes:</strong> 128 bits<br><strong>Structure:</strong> Substitution-Permutation Network<br><strong>Rounds:</strong> 10, 12, or 14<br><strong>Security:</strong> Not known to be efficiently breakable<br><strong>Efficiency:</strong> Fast enough for almost all applications (except for resource-constrained devices)<br><br><strong>Galois Counter Mode</strong><br><i>Can be used as a Stream Cipher</i><br><strong>Details:</strong> Variant of Counter mode, using the Galois mode of authentication<br><strong>Security:</strong> Supports IND-CPA if cipher is a PRF<br><strong>Efficiency:</strong> Faster for almost all applications"},
            { "name":"IDEA", "id":"a8", "url":"https://en.wikipedia.org/wiki/International_Data_Encryption_Algorithm", "blurb":"<i>International Data Encryption Algorithm (IDEA) is a symmetric key block cipher intended as a replacement for DES. It has been used in Pretty Good Privacy (PGP) v2.0 and is an optional algorithm in the OpenPGP standard. The patents for IDEA have expired and the algorithm is now free for all uses.</i><br><strong>Key Sizes:</strong> 128 bits<br><strong>Block Sizes:</strong> 64 bits<br><strong>Structure:</strong> Lai-Massey Scheme<br><strong>Rounds:</strong> 8.5<br><strong>Security:</strong> Not known to be efficiently breakable. However, the simple key schedule makes IDEA subject to a class of weak keys (these were remedied by XORing each subkey with a 16-bit constant)<br><strong>Efficiency:</strong> Fast enough for almost all applications (except for resource-constrained devices)"},
            { "name":"SIMON", "id":"a9", "url":"https://en.wikipedia.org/wiki/Simon_%28cipher%29#:~:text=Description%20of%20the%20cipher%20The%20Simon%20block%20cipher,implementation%20is%20denoted%20as%20Simon2%20n%20%2F%20nm.", "blurb":"<i>SIMON is a family of lightweight block ciphers released by the U.S. National Security Agency and optimized for performance in hardware-based implementations. SIMON is denoted with a n-bit word, a block length of 2n, and a key length which is a multiple of n by m, m = {2, 3, or 4}. SIMON implementation is thus denoted as SIMON 2n/nm.</i><br><strong>Key Sizes:</strong> 64, 72, 96, 128, 144, 192, or 256 bits<br><strong>Block Sizes:</strong> 32, 48, 64, 96, or 128 bits<br><strong>Structure:</strong> Feistel Network<br><strong>Rounds:</strong> 32, 36, 42, 44, 52, 54, 68, 69, or 72<br><strong>Security:</strong> Breakable with large-computation attack especially on reduced round variants of SIMON. The best published attacks on SIMON involve differential cryptanalysis attacks and are only marginally faster than brute-force attacks<br><strong>Efficiency:</strong> Faster than AES, targeting usage on resource-constrained devices"},
            { "name":"Twofish", "id":"a10", "url":"https://www.schneier.com/academic/twofish/", "blurb":"<i>Twofish is a symmetric key block cipher and one of the five finalists of the Advanced Encryption Standard contest, intended as a replacement for DES. Twofish uses a Feistel network along with a series of precomputed key-dependent S-boxes and a complex key schedule. Twofish has not been patented and has been placed in the public domain.</i><br><strong>Key Sizes:</strong> 128, 192, or 256 bits<br><strong>Block Sizes:</strong> 128 bits<br><strong>Structure:</strong> Feistel Network<br><strong>Rounds:</strong> 16<br><strong>Security:</strong> Not known to be efficiently breakable. A cryptanalysis claims that it will take roughly 2^51 chosen plaintexts to find a good pair of truncated differentials<br><strong>Efficiency:</strong> Significantly slower than AES"},
            { "name":"RC4", "id":"a11", "url":"https://en.wikipedia.org/wiki/RC4", "blurb":"<i>Rivest Cipher 4 is an extremely simple and efficient stream cipher. While no longer used, RC4 was known for its use in insecure protocols such as WEP.</i><br><strong>Key Sizes:</strong> 40 to 2048 bits<br><strong>Rounds:</strong> 1<br><strong>Security:</strong> RC4 uses a pseudorandom keystream, initialized with a variable length key using a key scheduling algorithm. RC4 is probably well known for its weaknesses in one of its implementations in WEP. The keystream generated by RC4 was found to be biased towards certain sequences making it vulnerable to distinguishing attacks<br><strong>Efficiency:</strong> Highly efficient"},
            { "name":"RC5", "id":"a12", "url":"https://en.wikipedia.org/wiki/RC5", "blurb":"<i>Rivest Cipher 5 is a symmetric key block cipher that uses data-dependent rotations in a Feistel-like Network.</i><br><strong>Key Sizes:</strong> 0 to 2040 bits<br><strong>Block Sizes:</strong> 32, 64, or 128 bits<br><strong>Structure:</strong> Feistel-like Network<br><strong>Rounds:</strong> 1 to 255<br><strong>Security:</strong> Not efficiently breakable. Researchers have been able to perform a differential attack against a 12-round RC5 with 64 bit blocks using 2^44 chosen plaintexts. Accordingly, the developers claim that 18-20 rounds are sufficient protection<br><strong>Efficiency:</strong> Efficient due to general structure of the algorithm and its use of primitive computer operations like XOR and shift."},
            { "name":"RC6", "id":"a13", "url":"https://en.wikipedia.org/wiki/RC6", "blurb":"<i>Rivest Cipher 6 is a symmetric key block cipher and one of the five finalists of the Advanced Encryption Standard contest, intended as a replacement for DES. The cipher is similar to RC5, uses data dependent rotations, modular addition, and XOR operations. RC6 has been likened to interweaving two parallel RC5 encryption processes. While developed by RSA Security, the patents for RC6 expired between 2015 and 2017.</i><br><strong>Key Sizes:</strong> 128, 192, or 256 bits<br><strong>Block Sizes:</strong> 128 bits<br><strong>Structure:</strong> Feistel Network<br><strong>Rounds:</strong> 20<br><strong>Security:</strong> Not efficiently breakable<br><strong>Efficiency:</strong> Less than RC5"},
            { "name":"One Time Pad", "id":"a14", "url":"https://en.wikipedia.org/wiki/One-time_pad", "blurb":"<i>In order to be a true OTP: 1) the key must be truly random (not pseudorandom); 2) the key must be as long if not longer than the plaintext; 3) they key cannot be reused in whole or in part; and 4) the key must be kept secret.</i><br><strong>Security:</strong> When those conditions are met, the OTP maintains the property of perfect secrecy<br><strong>Efficiency:</strong> Generating true randomness for the key is highly inefficient but the XOR operation against the message is extremely efficient."},
            { "name":"No soup for you!", "id":"a15", "url":"https://knowyourmeme.com/memes/no-soup-for-you-soup-nazi"}
        ]};

    for(let sch in schemes['schemes']) {

        if(schemes['schemes'][sch]['id'] == scheme_id)
            return schemes['schemes'][sch];
    }
    return scheme;
}
function goto_answers() {
    htmlstr = "After evaluating your answers, the cryptographic schemes you should pursue are:<p>";
    if(chosen_answers.length > 0)
    {
        htmlstr += "<ul>";
        for(let ans in chosen_answers) {
            scheme = getscheme(chosen_answers[ans]);
            if(scheme && scheme['id'] != 'a15'){
                htmlstr += "<li><a href='" + scheme['url'] + "'>" + scheme['name'] + "</a><div class='blurb'>" + scheme['blurb'] + "</div></li>";
            }
        }
        htmlstr += "</ul>";
    } else {
        htmlstr = "Oops!  Looks like we couldn't find a good match with your requested parameters!"
    }
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
