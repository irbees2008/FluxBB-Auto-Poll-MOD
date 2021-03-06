##
##        Mod title:  Auto Poll
##
##       Mod version:  1.2.2
##   Works on FluxBB:  1.5.*
##      Release date:  2009-09-07
##       Review date:  2015-12-22
##            Author:  Koos (pampoen10@yahoo.com)
##   Original Author:  Mediator (med_mediator@hotmail.com)
##      Contributors:  BN (http://la-bnbox.fr), Ishimaru Chiaki (http://ishimaru.pingveno.net)
##
##       Description:  Adds poll system to your forum. When posting a new topic,
##                    an extra topic option allows you to add a poll to it.
##                    Multiple votes can be allowed, and users can send a null vote.
##                    Ajoute un système de sondage sur votre forum.  Lorsque vous postez un nouveau sujet,
##                    une option de sujet supplémentaire vous permet d'ajouter un sondage.
##                    Le vote multiple peut être autorisée, et les utilisateurs peuvent voter blanc.
##
##         Languages:  English and French
##
##      Repositories:  https://github.com/MissGeek/FluxBB-Auto-Poll-MOD
##                     http://fluxbb.fr/forums/viewtopic.php?id=12219
##                     http://fluxbb.org/resources/mods/auto-poll-v110/
##                     http://fluxbb.org/forums/viewtopic.php?id=5569
##
##               Note:  If you are using another language, feel free to provide a translation for this MOD.
##
##    Affected files:  moderate.php
##                     post.php
##                     edit.php
##                     search.php
##                     viewforum.php
##                     viewtopic.php
##                     include/functions.php
##
##        Affects DB:  New tables:
##                        'polls'
##                     New column in 'topics' table:
##                        'question'
##                     New column in 'forum_perms' table:
##                        'post_polls'
##                     New options in 'config' table:
##                        'o_poll_enabled'
##                        'o_poll_max_fields'
##                        'o_poll_mod_delete_polls'
##                        'o_poll_mod_edit_polls'
##                        'o_poll_mod_reset_polls'
##
##        DISCLAIMER:  Please note that "mods" are not officially supported by
##                     PunBB. Installation of this modification is done at your
##                     own risk. Backup your forum database and any and all
##                     applicable files before proceeding.
##


#
#---------[ 1. UPLOAD ]-------------------------------------------------
#

files/install_mod.php to /install_mod.php
files/poll_add.php to /poll_add.php
files/poll_misc.php to /poll_misc.php
files/poll_vote.php to /poll_vote.php
files/include/poll to /include/poll
files/plugins/AP_Polls.php to /plugins/AP_Polls.php
files/lang/English/poll.php to /lang/English/poll.php
files/lang/English/admin_plugin_polls.php to /lang/English/admin_plugin_polls.php
files/lang/French/poll.php to /lang/French/poll.php
files/lang/French/admin_plugin_polls.php to /lang/French/admin_plugin_polls.php

#
#---------[ 2. RUN ]----------------------------------------------------------
#

install_mod.php


#
#---------[ 3. DELETE ]-------------------------------------------------------
#

install_mod.php


#
#---------[ 4. OPEN ]---------------------------------------------------------
#

moderate.php


#
#---------[ 5. FIND (line: 580) ]---------------------------------------------
#
		// Merge the posts into the topic
		$db->query('UPDATE '.$db->prefix.'posts SET topic_id='.$merge_to_tid.' WHERE topic_id IN('.implode(',', $topics).')') or error('Unable to merge the posts into the topic', __FILE__, __LINE__, $db->error());

#
#---------[ 6. AFTER, ADD ]---------------------------------------------------
#

		// POLL MOD: Delete polls
		$db->query('DELETE FROM '.$db->prefix.'polls WHERE pollid IN('.$topics.')') or error('Unable to delete poll', __FILE__, __LINE__, $db->error());


#
#---------[ 7. FIND (line: 897) ]---------------------------------------------
#
	// Select topics
	$result = $db->query('SELECT id, poster, subject, posted, last_post, last_post_id, last_poster, num_views, num_replies, closed, sticky, moved_to FROM '.$db->prefix.'topics WHERE id IN('.implode(',', $topic_ids).') ORDER BY sticky DESC, '.$sort_by.', id DESC') or error('Unable to fetch topic list for forum', __FILE__, __LINE__, $db->error());


#
#---------[ 8. REPLACE WITH ]---------------------------------------------------
#
	// Select topics
	$result = $db->query('SELECT id, poster, subject, posted, last_post, last_post_id, last_poster, num_views, num_replies, closed, sticky, moved_to, question FROM '.$db->prefix.'topics WHERE id IN('.implode(',', $topic_ids).') ORDER BY sticky DESC, '.$sort_by.', id DESC') or error('Unable to fetch topic list for forum', __FILE__, __LINE__, $db->error());

#
#---------[ 9. FIND (line: 924) ]---------------------------------------------
#

		if ($cur_topic['sticky'] == '1')


#
#---------[ 10. BEFORE, ADD ]---------------------------------------------------
#

		// POLL MOD:
		if (file_exists(PUN_ROOT.'lang/'.$pun_user['language'].'/poll.php'))
			require PUN_ROOT.'lang/'.$pun_user['language'].'/poll.php';
		else
			require PUN_ROOT.'lang/English/poll.php';
			
		if ($cur_topic['question'] != '')
			$subject = $lang_poll['Poll'] . ': '.$subject;


#
#---------[ 11. OPEN ]---------------------------------------------------------
#

post.php


#
#---------[ 12. FIND (line: 449) ]---------------------------------------------
#

		redirect('viewtopic.php?pid='.$new_pid.'#p'.$new_pid, $lang_post['Post redirect']);


#
#---------[ 13. BEFORE, ADD ]---------------------------------------------------
#

		require PUN_ROOT.'include/poll/poll_post.php';


#
#---------[ 14. FIND (line: 674) ]---------------------------------------------
#

if (!$pun_user['is_guest'])
{
	if ($pun_config['o_smilies'] == '1')
		$checkboxes[] = '<label><input type="checkbox" name="hide_smilies" value="1" tabindex="'.($cur_index++).'"'.(isset($_POST['hide_smilies']) ? ' checked="checked"' : '').' />'.$lang_post['Hide smilies'].'<br /></label>';


#
#---------[ 15. AFTER, ADD ]---------------------------------------------------
#

	require PUN_ROOT.'include/poll/poll_post.php';

#
#---------[ 11. OPEN ]---------------------------------------------------------
#

edit.php

#
#---------[ 12. FIND (line: 139) ]---------------------------------------------
#

		redirect('viewtopic.php?pid='.$id.'#p'.$id, $lang_post['Edit redirect']);


#
#---------[ 13. BEFORE, ADD ]---------------------------------------------------
#

		require PUN_ROOT.'include/poll/poll_edit.php';


#
#---------[ 14. FIND (line: 254) ]---------------------------------------------
#

		$checkboxes[] = '<label><input type="checkbox" name="hide_smilies" value="1" tabindex="'.($cur_index++).'" />'.$lang_post['Hide smilies'].'<br /></label>';
}

#
#---------[ 15. AFTER, ADD ]---------------------------------------------------
#

require PUN_ROOT.'include/poll/poll_edit.php';

#
#---------[ 16. OPEN ]---------------------------------------------------------
#

search.php


#
#---------[ 17. FIND (line: 15) ]---------------------------------------------
#

// Load the search.php language file
require PUN_ROOT.'lang/'.$pun_user['language'].'/search.php';


#
#---------[ 18. AFTER, ADD ]---------------------------------------------------
#

// POLL MOD: Load the poll.php language file
if (file_exists(PUN_ROOT.'lang/'.$pun_user['language'].'/poll.php'))
	require PUN_ROOT.'lang/'.$pun_user['language'].'/poll.php';
else
	require PUN_ROOT.'lang/English/poll.php';


#
#---------[ 19. FIND (line: 498) ]---------------------------------------------
#

		if ($show_as == 'posts')
			$result = $db->query('SELECT p.id AS pid, p.poster AS pposter, p.posted AS pposted, p.poster_id, p.message, p.hide_smilies, t.id AS tid, t.poster, t.subject, t.first_post_id, t.last_post, t.last_post_id, t.last_poster, t.num_replies, t.forum_id, f.forum_name FROM '.$db->prefix.'posts AS p INNER JOIN '.$db->prefix.'topics AS t ON t.id=p.topic_id INNER JOIN '.$db->prefix.'forums AS f ON f.id=t.forum_id WHERE p.id IN('.implode(',', $search_ids).') ORDER BY '.$sort_by_sql.' '.$sort_dir) or error('Unable to fetch search results', __FILE__, __LINE__, $db->error());
		else
			$result = $db->query('SELECT t.id AS tid, t.poster, t.subject, t.last_post, t.last_post_id, t.last_poster, t.num_replies, t.closed, t.sticky, t.forum_id, f.forum_name FROM '.$db->prefix.'topics AS t INNER JOIN '.$db->prefix.'forums AS f ON f.id=t.forum_id WHERE t.id IN('.implode(',', $search_ids).') ORDER BY '.$sort_by_sql.' '.$sort_dir) or error('Unable to fetch search results', __FILE__, __LINE__, $db->error());


#
#---------[ 20. REPLACE WITH ]---------------------------------------------------
#

		if ($show_as == 'posts')
			$result = $db->query('SELECT p.id AS pid, p.poster AS pposter, p.posted AS pposted, p.poster_id, p.message, p.hide_smilies, t.id AS tid, t.poster, t.subject, t.question, t.first_post_id, t.last_post, t.last_post_id, t.last_poster, t.num_replies, t.forum_id, f.forum_name FROM '.$db->prefix.'posts AS p INNER JOIN '.$db->prefix.'topics AS t ON t.id=p.topic_id INNER JOIN '.$db->prefix.'forums AS f ON f.id=t.forum_id WHERE p.id IN('.implode(',', $search_ids).') ORDER BY '.$sort_by_sql.' '.$sort_dir) or error('Unable to fetch search results', __FILE__, __LINE__, $db->error());
		else
			$result = $db->query('SELECT t.id AS tid, t.poster, t.subject, t.question, t.last_post, t.last_post_id, t.last_poster, t.num_replies, t.closed, t.sticky, t.forum_id, f.forum_name FROM '.$db->prefix.'topics AS t INNER JOIN '.$db->prefix.'forums AS f ON f.id=t.forum_id WHERE t.id IN('.implode(',', $search_ids).') ORDER BY '.$sort_by_sql.' '.$sort_dir) or error('Unable to fetch search results', __FILE__, __LINE__, $db->error());


#
#---------[ 21. FIND (line: 692) ]---------------------------------------------
#

				$subject = '<a href="viewtopic.php?id='.$cur_search['tid'].'">'.pun_htmlspecialchars($cur_search['subject']).'</a> <span class="byuser">'.$lang_common['by'].' '.pun_htmlspecialchars($cur_search['poster']).'</span>';


#
#---------[ 22. AFTER, ADD ]---------------------------------------------------
#

				// POLL MOD:
				if ($cur_search['question'] != '')
					$subject = $lang_poll['Poll'].': '.$subject;


#
#---------[ 23. FIND (line: 721) ]---------------------------------------------
#

					$subject_new_posts = null;


#
#---------[ 24. AFTER, ADD ]---------------------------------------------------
#

				// POLL MOD:
				if ($cur_search['question'] != '')
					$subject =  $lang_poll['Poll'].':  '.$subject;


#
#---------[ 25. OPEN ]---------------------------------------------------------
#

viewforum.php


#
#---------[ 26. FIND (line: 173) ]---------------------------------------------
#

	$result = $db->query($sql) or error('Unable to fetch topic list', __FILE__, __LINE__, $db->error());


#
#---------[ 27. BEFORE, ADD ]---------------------------------------------------
#

	require PUN_ROOT.'include/poll/poll_viewforum.php';


#
#---------[ 28. FIND (line: 224) ]---------------------------------------------
#

		// Insert the status text before the subject
		$subject = implode(' ', $status_text).' '.$subject;

#
#---------[ 29. BEFORE, ADD ]---------------------------------------------------
#

		require PUN_ROOT.'include/poll/poll_viewforum.php';


#
#---------[ 30. OPEN ]---------------------------------------------------------
#

viewtopic.php


#
#---------[ 31. FIND (line: 227) ]---------------------------------------------
#

// Retrieve the posts (and their respective poster/online status)


#
#---------[ 32. BEFORE, ADD ]---------------------------------------------------
#

require PUN_ROOT.'include/poll/poll_viewtopic.php';


#
#---------[ 33. OPEN ]---------------------------------------------------------
#

include/functions.php


#
#---------[ 34. FIND (line: 741) ]---------------------------------------------
#

	// Create a list of the post IDs in this topic


#
#---------[ 35. BEFORE, ADD ]---------------------------------------------------
#

	// POLL MOD: Delete the poll
	$db->query('DELETE FROM '.$db->prefix.'polls WHERE pollid='.$topic_id) or error('Unable to delete poll', __FILE__, __LINE__, $db->error());

