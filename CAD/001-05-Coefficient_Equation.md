# Coefficient / Equation
```csharp
namespace Geometry
{
  public struct Coefficient(int position, double value) : IEquatable<Coefficient>
  {
    public int Pos = position;
    public double Val = value;

    public bool Equals(Coefficient other) => this.Pos == other.Pos;

    public override string ToString()
    {
      return string.Format("{0}, {1}", (object) this.Pos, (object) this.Val);
    }
  }

  public class Equation
  {
    public List<Coefficient> Coefficients;

    public Equation()
    {
    }

    public Equation(int size) => this.Coefficients = new List<Coefficient>(size);

    public void Add(int pos, double val)
    {
      Coefficient coefficient = new Coefficient(pos, val);
      int index1 = this.Coefficients.IndexOf(coefficient);
      if (index1 == -1)
      {
        int count = this.Coefficients.Count;
        for (int index2 = 0; index2 < count; ++index2)
        {
          if (this.Coefficients[index2].Pos > coefficient.Pos)
            this.Coefficients.Insert(index2 - 1, coefficient);
        }
        if (this.Coefficients.Count != count)
          return;
        this.Coefficients.Add(coefficient);
      }
      else
        this.Coefficients[index1] = new Coefficient(coefficient.Pos, this.Coefficients[index1].Val + val);
    }

    public void RemoveAt(int pos)
    {
      int index1 = this.Coefficients.IndexOf(new Coefficient(pos, 0.0));
      if (index1 == -1)
      {
        if (this.Coefficients[0].Pos <= pos)
          return;
        for (int index2 = 0; index2 < this.Coefficients.Count; ++index2)
        {
          Coefficient coefficient = this.Coefficients[index2];
          this.Coefficients[index2] = new Coefficient(coefficient.Pos - 1, coefficient.Val);
        }
      }
      else
      {
        for (int index3 = 0; index3 < this.Coefficients.Count; ++index3)
        {
          Coefficient coefficient = this.Coefficients[index3];
          if (coefficient.Pos > pos)
            this.Coefficients[index3] = new Coefficient(coefficient.Pos - 1, coefficient.Val);
        }
        this.Coefficients.RemoveAt(index1);
      }
    }

    public Coefficient this[int pos]
    {
      get
      {
        Coefficient coefficient = new Coefficient(pos, 0.0);
        int index = this.Coefficients.IndexOf(coefficient);
        if (index != -1)
          return this.Coefficients[index];
        coefficient.Pos = -1;
        return coefficient;
      }
    }

    public double MultiplyBy(double[] x)
    {
      double num = 0.0;
      int count = this.Coefficients.Count;
      for (int index = 0; index < count; ++index)
      {
        Coefficient coefficient = this.Coefficients[index];
        num += coefficient.Val * x[coefficient.Pos];
      }
      return num;
    }

    public double MultiplyBy(int rowIndex, int[] I, List<int> J, List<double> A, double[] x)
    {
      double num1 = 0.0;
      int num2 = I[rowIndex + 1] - I[rowIndex];
      for (int index = 0; index < num2; ++index)
        num1 += A[rowIndex + index] * x[J[rowIndex + index]];
      return num1;
    }

    public override string ToString()
    {
      return string.Format("{0}", (object) this.Coefficients.Count);
    }
  }
}

```