# WildcardTest.Console

Test application for a wildcard

```cs
public static bool WildcardCharacters(string str)
{
	var tokens = str.Split(' ');

	if (tokens.Length != 2)
	{
		throw new Exception("Input string should have only one space");
	}
	var wildcard = tokens[0];
	var text = tokens[1];

	var wildCursor = -1;
	var textCursor = 0;
	var prevChar = '-';
	while (++wildCursor < wildcard.Length)
	{
		var wildChar = wildcard[wildCursor];
		if (wildChar == '+')
		{
			var ch = (textCursor < text.Length) ? text[textCursor++] : '\0';
			if (!Char.IsLetter(ch) || prevChar == ch) { return false; }
			prevChar = ch;
		}
		else if (wildChar == '*')
		{
			var count = 3;
			// set a different count
			if (wildCursor + 1 < wildcard.Length && wildcard[wildCursor + 1] == '{')
			{
				wildCursor++;
				var strCount = "";
				while (wildcard[++wildCursor] != '}')
				{
					strCount += wildcard[wildCursor];
					if (wildCursor >= wildcard.Length) throw new Exception("Closing brackets '}' are missing");
				}

				count = int.Parse(strCount);
			}

			// compare '*' wildcard
			var firstChar = text[textCursor];

			// if first character is not letter, then is not match
			if (!Char.IsLetter(firstChar) || prevChar == firstChar) { return false; }
			prevChar = firstChar;

			for (int i = 0; i < count; i++)
			{
				var ch = (textCursor < text.Length) ? text[textCursor++] : '\0';
				// all characters should be the same a first char
				if (ch != firstChar) { return false; }
			}
		}
	}

	// make sure we analyzed entire text
	return textCursor == text.Length;
}

static void Main(string[] args)
{
	// true
	Console.WriteLine(WildcardCharacters("++* dmkkk"));
	Console.WriteLine(WildcardCharacters("++ dm"));
	Console.WriteLine(WildcardCharacters("**+*{2} mmmrrrkbb"));
	Console.WriteLine(WildcardCharacters("+++++** abcdemmmlll"));
	Console.WriteLine(WildcardCharacters("+++++*{5} abcdemmmmm"));
	Console.WriteLine(WildcardCharacters("**+*{2} mmmrrrkbb"));

	// false 
	Console.WriteLine(WildcardCharacters("+++++* abcdemmmmmm"));
}

```