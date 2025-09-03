# Matrix
```csharp
namespace Geometry
{
  public class Matrix
  {
    public static bool IsIdentity4(double[,] matrix)
    {
      return matrix[0, 0] == 1.0 && matrix[0, 1] == 0.0 && matrix[0, 2] == 0.0 
      && matrix[0, 3] == 0.0 && matrix[1, 0] == 0.0 && matrix[1, 1] == 1.0 
      && matrix[1, 2] == 0.0 && matrix[1, 3] == 0.0 && matrix[2, 0] == 0.0 
      && matrix[2, 1] == 0.0 && matrix[2, 2] == 1.0 && matrix[2, 3] == 0.0 
      && matrix[3, 0] == 0.0 && matrix[3, 1] == 0.0 && matrix[3, 2] == 0.0 
      && matrix[3, 3] == 1.0;
    }

    public static void Multiply(double scalar, ref double[,] matrix)
    {
      int length1 = matrix.GetLength(0);
      int length2 = matrix.GetLength(1);
      for (int index1 = 0; index1 < length1; ++index1)
      {
        for (int index2 = 0; index2 < length2; ++index2)
          matrix[index1, index2] *= scalar;
      }
    }

    public static double[] Multiply3x(double[,] a, double[] b)
    {
      double[] numArray = new double[3];
      for (int index1 = 0; index1 < 3; ++index1)
      {
        for (int index2 = 0; index2 < 3; ++index2)
          numArray[index1] += a[index1, index2] * b[index2];
      }
      return numArray;
    }

    public static double[] Multiply4x(double[,] a, double[] b)
    {
      double[] numArray = new double[4];
      for (int index1 = 0; index1 < 4; ++index1)
      {
        for (int index2 = 0; index2 < 4; ++index2)
          numArray[index1] += a[index1, index2] * b[index2];
      }
      return numArray;
    }

    public static double[] Multiply(double[,] a, double[] b)
    {
      double[] numArray = new double[b.Length];
      for (int index1 = 0; index1 < a.GetLength(0); ++index1)
      {
        for (int index2 = 0; index2 < a.GetLength(1); ++index2)
          numArray[index1] += a[index1, index2] * b[index2];
      }
      return numArray;
    }

    public static double[] MultiplyX3(double[] a, double[,] b)
    {
      double[] numArray = new double[3];
      for (int index1 = 0; index1 < 3; ++index1)
      {
        for (int index2 = 0; index2 < 3; ++index2)
          numArray[index1] += a[index2] * b[index2, index1];
      }
      return numArray;
    }

    internal static double[] Multiply4x4(double[] _param0, double[,] _param1)
    {
      double[] numArray = new double[4];
      for (int index1 = 0; index1 < 4; ++index1)
      {
        for (int index2 = 0; index2 < 4; ++index2)
          numArray[index1] += _param0[index2] * _param1[index2, index1];
      }
      return numArray;
    }

    public static double[,] Multiply4x4(double[,] a, double[,] b)
    {
      double[,] numArray = new double[4, 4];
      for (int index1 = 0; index1 < 4; ++index1)
      {
        for (int index2 = 0; index2 < 4; ++index2)
        {
          for (int index3 = 0; index3 < 4; ++index3)
            numArray[index1, index2] += a[index1, index3] * b[index3, index2];
        }
      }
      return numArray;
    }

    [CLSCompliant(false)]
    public static double[,] Multiply(double[,] a, double[,] b)
    {
      int length1 = a.GetLength(0);
      int length2 = a.GetLength(1);
      int length3 = b.GetLength(1);
      double[,] numArray = new double[length1, length3];
      for (int index1 = 0; index1 < length1; ++index1)
      {
        for (int index2 = 0; index2 < length3; ++index2)
        {
          for (int index3 = 0; index3 < length2; ++index3)
            numArray[index1, index2] += a[index1, index3] * b[index3, index2];
        }
      }
      return numArray;
    }

    internal static Equation[] MergeEquation(Equation[] _param0, Equation[] _param1)
    {
      Equation[] equationArray = new Equation[_param0.Length];
      int length = _param0.Length;
      for (int index1 = 0; index1 < length; ++index1)
      {
        equationArray[index1] = new Equation()
        {
          Coefficients = new List<Coefficient>()
        };
        for (int pos = 0; pos < length; ++pos)
        {
          for (int index2 = 0; index2 < _param0[index1].Coefficients.Count; ++index2)
          {
            Coefficient coefficient1 = _param0[index1].Coefficients[index2];
            Coefficient coefficient2 = _param1[coefficient1.Pos][pos];
            if (coefficient2.Pos != -1)
              equationArray[index1].Add(pos, coefficient1.Val * coefficient2.Val);
          }
        }
      }
      return equationArray;
    }

    public static double[,] Multiply(Equation[] a, Equation[] b, int nSize)
    {
      int num = nSize / 2;
      double[,] numArray = new double[a.Length, nSize];
      int length = a.Length;
      for (int index = 0; index < length; ++index)
      {
        for (int pos = 0; pos < length; ++pos)
        {
          foreach (Coefficient coefficient1 in a[index].Coefficients)
          {
            Coefficient coefficient2 = b[coefficient1.Pos][pos];
            if (coefficient2.Pos != -1)
              numArray[index, pos - index + num] += coefficient1.Val * coefficient2.Val;
          }
        }
      }
      return numArray;
    }

    public static double[,] Multiply3x3(double[,] a, double[,] b)
    {
      double[,] numArray = new double[3, 3];
      for (int index1 = 0; index1 < 3; ++index1)
      {
        for (int index2 = 0; index2 < 3; ++index2)
        {
          for (int index3 = 0; index3 < 3; ++index3)
            numArray[index1, index2] += a[index1, index3] * b[index3, index2];
        }
      }
      return numArray;
    }

    public static double[,] Transpose(double[,] matrix)
    {
      int length1 = matrix.GetLength(0);
      int length2 = matrix.GetLength(1);
      double[,] numArray = new double[length2, length1];
      for (int index1 = 0; index1 < length1; ++index1)
      {
        for (int index2 = 0; index2 < length2; ++index2)
          numArray[index2, index1] = matrix[index1, index2];
      }
      return numArray;
    }

    public static Equation[] Transpose(int n, int m, Equation[] equations)
    {
      Equation[] equationArray = new Equation[n];
      for (int index = 0; index < n; ++index)
        equationArray[index] = new Equation(0);
      for (int pos = 0; pos < m; ++pos)
      {
        for (int index = 0; index < equations[pos].Coefficients.Count; ++index)
        {
          Coefficient coefficient = equations[pos].Coefficients[index];
          equationArray[coefficient.Pos].Add(pos, coefficient.Val);
        }
      }
      return equationArray;
    }

    public static double Determinant3(double[,] matrix)
    {
      double num1 = matrix[2, 2];
      double num2 = matrix[1, 1];
      double num3 = matrix[1, 2];
      double num4 = matrix[1, 0];
      double num5 = matrix[2, 0];
      double num6 = matrix[2, 1];
      return matrix[0, 0] * (num2 * num1 - num6 * num3) 
        - matrix[0, 1] * (num4 * num1 - num5 * num3) 
        + matrix[0, 2] * (num4 * num6 - num5 * num2);
    }

    public static double Determinant2(double[,] matrix)
    {
      return matrix[0, 0] * matrix[1, 1] - matrix[0, 1] * matrix[1, 0];
    }

    public static bool Inverse3(double[,] matrix, out double[,] inverse)
    {
      inverse = new double[4, 4];
      double num = Matrix.Determinant3(matrix);
      if (Math.Abs(num) < 1E-12)
        return false;
      inverse[0, 0] = (matrix[1, 1] * matrix[2, 2] - matrix[1, 2] * matrix[2, 1]) / num;
      inverse[0, 1] = -(matrix[0, 1] * matrix[2, 2] - matrix[2, 1] * matrix[0, 2]) / num;
      inverse[0, 2] = (matrix[0, 1] * matrix[1, 2] - matrix[1, 1] * matrix[0, 2]) / num;
      inverse[1, 0] = -(matrix[1, 0] * matrix[2, 2] - matrix[1, 2] * matrix[2, 0]) / num;
      inverse[1, 1] = (matrix[0, 0] * matrix[2, 2] - matrix[2, 0] * matrix[0, 2]) / num;
      inverse[1, 2] = -(matrix[0, 0] * matrix[1, 2] - matrix[1, 0] * matrix[0, 2]) / num;
      inverse[2, 0] = (matrix[1, 0] * matrix[2, 1] - matrix[2, 0] * matrix[1, 1]) / num;
      inverse[2, 1] = -(matrix[0, 0] * matrix[2, 1] - matrix[2, 0] * matrix[0, 1]) / num;
      inverse[2, 2] = (matrix[0, 0] * matrix[1, 1] - matrix[0, 1] * matrix[1, 0]) / num;
      return true;
    }

    private static void Transpose(
      double[,] _param0,
      ref double[,] _param1,
      int _param2,
      int _param3)
    {
      for (int index1 = 0; index1 < 3; ++index1)
      {
        for (int index2 = 0; index2 < 3; ++index2)
        {
          int index3 = index1 + (index1 >= _param2 ? 1 : 0);
          int index4 = index2 + (index2 >= _param3 ? 1 : 0);
          _param1[index1, index2] = _param0[index3, index4];
        }
      }
    }

    public static double Determinant4(double[,] matrix)
    {
      double num1 = 0.0;
      double num2 = 1.0;
      double[,] matrix1 = new double[3, 3];
      int index = 0;
      while (index < 4)
      {
        Matrix.Transpose(matrix, ref matrix1, 0, index);
        double num3 = Matrix.Determinant3(matrix1);
        num1 += matrix[0, index] * num3 * num2;
        ++index;
        num2 *= -1.0;
      }
      return num1;
    }

    public static bool Inverse4(double[,] matrix, out double[,] inverse)
    {
      inverse = new double[4, 4];
      double num1 = Matrix.Determinant4(matrix);
      double[,] matrix1 = new double[3, 3];
      if (Math.Abs(num1) < 1E-12)
        return false;
      for (int index1 = 0; index1 < 4; ++index1)
      {
        for (int index2 = 0; index2 < 4; ++index2)
        {
          int num2 = 1 - (index1 + index2) % 2 * 2;
          Matrix.Transpose(matrix, ref matrix1, index1, index2);
          inverse[index2, index1] = Matrix.Determinant3(matrix1) * (double) num2 / num1;
        }
      }
      return true;
    }

    public static double[,] Inverse4(double[,] a)
    {
      double num1 = a[0, 0] * a[1, 1] - a[1, 0] * a[0, 1];
      double num2 = a[0, 0] * a[1, 2] - a[1, 0] * a[0, 2];
      double num3 = a[0, 0] * a[1, 3] - a[1, 0] * a[0, 3];
      double num4 = a[0, 1] * a[1, 2] - a[1, 1] * a[0, 2];
      double num5 = a[0, 1] * a[1, 3] - a[1, 1] * a[0, 3];
      double num6 = a[0, 2] * a[1, 3] - a[1, 2] * a[0, 3];
      double num7 = a[2, 2] * a[3, 3] - a[3, 2] * a[2, 3];
      double num8 = a[2, 1] * a[3, 3] - a[3, 1] * a[2, 3];
      double num9 = a[2, 1] * a[3, 2] - a[3, 1] * a[2, 2];
      double num10 = a[2, 0] * a[3, 3] - a[3, 0] * a[2, 3];
      double num11 = a[2, 0] * a[3, 2] - a[3, 0] * a[2, 2];
      double num12 = a[2, 0] * a[3, 1] - a[3, 0] * a[2, 1];
      double num13 = 1.0 / (num1 * num7 - num2 * num8 + num3 
        * num9 + num4 * num10 - num5 * num11 + num6 * num12);
      return new double[4, 4]
      {
        {
          (a[1, 1] * num7 - a[1, 2] * num8 + a[1, 3] * num9) * num13,
          (-a[0, 1] * num7 + a[0, 2] * num8 - a[0, 3] * num9) * num13,
          (a[3, 1] * num6 - a[3, 2] * num5 + a[3, 3] * num4) * num13,
          (-a[2, 1] * num6 + a[2, 2] * num5 - a[2, 3] * num4) * num13
        },
        {
          (-a[1, 0] * num7 + a[1, 2] * num10 - a[1, 3] * num11) * num13,
          (a[0, 0] * num7 - a[0, 2] * num10 + a[0, 3] * num11) * num13,
          (-a[3, 0] * num6 + a[3, 2] * num3 - a[3, 3] * num2) * num13,
          (a[2, 0] * num6 - a[2, 2] * num3 + a[2, 3] * num2) * num13
        },
        {
          (a[1, 0] * num8 - a[1, 1] * num10 + a[1, 3] * num12) * num13,
          (-a[0, 0] * num8 + a[0, 1] * num10 - a[0, 3] * num12) * num13,
          (a[3, 0] * num5 - a[3, 1] * num3 + a[3, 3] * num1) * num13,
          (-a[2, 0] * num5 + a[2, 1] * num3 - a[2, 3] * num1) * num13
        },
        {
          (-a[1, 0] * num9 + a[1, 1] * num11 - a[1, 2] * num12) * num13,
          (a[0, 0] * num9 - a[0, 1] * num11 + a[0, 2] * num12) * num13,
          (-a[3, 0] * num4 + a[3, 1] * num2 - a[3, 2] * num1) * num13,
          (a[2, 0] * num4 - a[2, 1] * num2 + a[2, 2] * num1) * num13
        }
      };
    }
  }
}
```
