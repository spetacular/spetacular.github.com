---
layout: post
title: Coding Tips -- Compare The Version Numbers Between Two Releases
category : php
tagline: "Supporting tagline"
tags : [版本控制]
---

Given two version numbers, How to check which release is newer ? For example:

1.1 is newer than 1.0 (1.1 > 1.0);

1.0.0 is equal to 1.0(1.0.0 = 1.0);

0.9 is older than 1.0(0.9 < 1.0).

Function implement is as below:
	
	/**
	* Compare two versions.
	* @param string $newVersion
	* @param string $oldVersion
	* @return int. 1 represents '>',0 represents '=',-1 represents '<'
	*/
    function versionCompare($newVersion,$oldVersion)
    {
        $newArray = explode(".",$newVersion);
        $oldArray = explode(".",$oldVersion);
        do{
            $v1 = array_shift($newArray);
            $v2 = array_shift($oldArray);
            if($v1 == NULL){
                $v1 = 0;
            }

            if($v2 == NULL){
                $v2 = 0;
            }
            if($v1 > $v2){
                return 1;
            }else if($v1 < $v2){
                return -1;
            }
        }while(count($newArray) > 0 || count($oldArray) > 0);

        return 0;
    }

Test Cases is as below:
	
	var_dump(versionCompare('',''));		// =	
	var_dump(versionCompare('1.0','1.0'));		// =
	var_dump(versionCompare('1.1','1.0')); 		// >
	var_dump(versionCompare('0.9','1.0'));		// <
	var_dump(versionCompare('1.0.0','1.0'));	// =
	var_dump(versionCompare('1.0.1','1.0'));	// >
	var_dump(versionCompare('1.0.1','1.0.2'));	// <