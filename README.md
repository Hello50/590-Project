# 590-Project


1. User Enumeration
  - [ ] Summary: Find the user name via user enumeration feauture on Wpscan.
    - Vulnerability types:
    - Tested in version:4.2
    - Fixed in version: ?
  - [ ] GIF Walkthrough: ![userenumeration](https://user-images.githubusercontent.com/37880152/40150460-92f577de-592e-11e8-8e8b-2fa520d3f1a7.gif)
  - [ ] Steps to recreate:  Finding the username via the Enumerate Username feauture on the wpscan use the command: "wpscan --url http://wpdistillery.vm enumerate -u". The –url argument is used to specify the target website. The –enumerate argument is used to trigger the WPScan enumeration module and the -u is to enumerate WordPress users. 
 - [ ] Affected source code:



1. Password Bruteforce
  - [ ] Summary: The two gifs above show how you can use the same Wpscan tool to steal login credentials using the found username found using the enumerate username feauture and then running a bruteforce attack using a common password text file found online.
    - Vulnerability types:
    - Tested in version:4.2
    - Fixed in version: 
  - [ ] GIF Walkthrough:
  ![credential theft pt 1](https://user-images.githubusercontent.com/37880152/40150494-c2b45ab2-592e-11e8-8982-e23dcffa8846.gif)
![credential theft pt 2](https://user-images.githubusercontent.com/37880152/40150495-c462f044-592e-11e8-86a0-e6b36552bac5.gif)
  - [ ] Steps to recreate: The command used is: "wpscan --url http://wpdistillery.vm --wordlist 'root/Documents/john.txt' " This version of wordpress does not have bruteforce counter measures such as device cookies to slow down the process or lockout after certain passwords attempted therefore, it is susceptible to bruteforcing. The other countermeasure would be for a user to use a strong password that is not common.
  - [ ] Affected source code:


1. SQL Injection
  - [ ] Summary: This exploit uses a SQL query to bypass the comment approval which is typically done through the admin comments page and is generally only accessible when logged in. 
    - Vulnerability types: SQLI Injection/Privilege Escalation
    - Tested in version: 4.2
    - Fixed in version: 4.2.4 
  - [ ] GIF Walkthrough: ![privilege escalation pt 1](https://user-images.githubusercontent.com/37880152/40150498-ca343cf8-592e-11e8-84cd-499ff4d8d5b5.gif)

  - [ ] Steps to recreate:
  Wordpress runs with a mysql database you can use Sql to query the database to approve any comments made after the submitted SQL exploit query. 
I inserted the following command as a comment after that was approved any comment following it is automatically approved:

foreach ( $group_by_status as $status => $comments ) {
// Sanity check. This shouldn’t happen.
– if ( ‘post-trashed’ == $status )
+ if ( ‘post-trashed’ == $status ) {
$status = ‘0’;
– $comments_in = implode( “‘, ‘”, $comments );
– $wpdb->query( “UPDATE $wpdb->comments SET comment_approved = ‘$status’ WHERE comment_ID IN (‘” . $comments_in . “‘)” );
+ }
+ $comments_in = implode( ‘, ‘, array_map( ‘intval’, $comments ) );
+ $wpdb->query( $wpdb->prepare( “UPDATE $wpdb->comments SET comment_approved = %s WHERE comment_ID IN ($comments_in)”, $status ) );
}

clean_comment_cache( array_keys($statuses) );
 
  - [ ] Affected source code:
<div class="comment-content">
                    <p>foreach ( $group_by_status as $status =&gt; $comments ) {<br />
         // Sanity check. This shouldn&#8217;t happen.<br />
&#8211;        if ( &#8216;post-trashed&#8217; == $status )<br />
+        if ( &#8216;post-trashed&#8217; == $status ) {<br />
             $status = &#8216;0&#8217;;<br />
&#8211;        $comments_in = implode( &#8220;&#8216;, &#8216;&#8221;, $comments );<br />
&#8211;        $wpdb-&gt;query( &#8220;UPDATE $wpdb-&gt;comments SET comment_approved = &#8216;$status&#8217; WHERE comment_ID IN (&#8216;&#8221; . $comments_in . &#8220;&#8216;)&#8221; );<br />
+        }<br />
+        $comments_in = implode( &#8216;, &#8216;, array_map( &#8216;intval&#8217;, $comments ) );<br />
+        $wpdb-&gt;query( $wpdb-&gt;prepare( &#8220;UPDATE $wpdb-&gt;comments SET comment_approved = %s WHERE comment_ID IN ($comments_in)&#8221;, $status ) );<br />
     }</p>
<p>     clean_comment_cache( array_keys($statuses) );</p>
                </div><!-- .comment-content -->








