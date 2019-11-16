# snippets

OBFUSCATE EMAILS

Override 'the_content' Wordpress function
<pre>
add_filter( 'the_content', 'custom_filter_the_content' );
function custom_filter_the_content( $content ) {
    $string = '';
    $output = '';
    ob_start();
    echo $content;
    $string = ob_get_contents();
    ob_end_clean();
    if($string) {
        $emails_matched = extract_all_emails($string);
        if($emails_matched) {
            foreach($emails_matched as $em) {
                $encrypted = antispambot($em,1);
                $replace = 'mailto:'.$em;
                $new_mailto = 'mailto:'.$encrypted;
                $string = str_replace($replace, $new_mailto, $string);
                $rep2 = $em.'</a>';
                $new2 = antispambot($em).'</a>';
                $string = str_replace($rep2, $new2, $string);
            }
        }
    }
    return $string;
}

function extract_all_emails($string){
    $pattern = '/[a-z0-9_\-\+\.]+@[a-z0-9\-]+\.([a-z]{2,4})(?:\.[a-z]{2})?/i';
    preg_match_all($pattern, $string, $matches);
    $result = ( isset($matches[0]) && array_filter($matches[0]) ) ? $matches[0] : '';
    return $result;
}


/* 
This function can only be used on custom fields (e.g. ACF plugin)
Usage: 
$text = get_field('myfieldname');
$content = email_obfuscator($text);
*/

function email_obfuscator($string) {
    $output = '';
    if($string) {
        $emails_matched = ($string) ? extract_all_emails($string) : '';
        if($emails_matched) {
            foreach($emails_matched as $em) {
                $encrypted = antispambot($em,1);
                $replace = 'mailto:'.$em;
                $new_mailto = 'mailto:'.$encrypted;
                $string = str_replace($replace, $new_mailto, $string);
                $rep2 = $em.'</a>';
                $new2 = antispambot($em).'</a>';
                $string = str_replace($rep2, $new2, $string);
            }
        }
        $output = apply_filters('the_content',$string);
    }
    return $output;
}
</pre>
